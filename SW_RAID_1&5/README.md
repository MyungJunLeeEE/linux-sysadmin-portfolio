# 🛡️ Linux Software RAID (1 & 5) 구현 및 장애 복구(Fault Tolerance & Recovery) 실습

가상머신 환경에서 소프트웨어 RAID(`mdadm`)를 활용하여 디스크 이중화(RAID 1)와 분산 패리티(RAID 5)를 직접 구축하고, **디스크 장애 발생 시 데이터 무결성을 유지하며 복구하는 프로세스**를 검증한 실습 기록입니다.

---

## 🛠️ 실습 환경 (Environment)
* **OS:** Rocky Linux
* **도구:** `mdadm`, `fdisk`, `mkfs.ext4`, `mount`
* **저장장치:** 추가 가상 디스크 (SCSI)

---

# 🛡️ Linux Software RAID 1 (미러링) 구축 및 장애 복구 실습

가상머신(VMware) 환경에서 추가 디스크(`sdb`, `sdc`)를 활용해 **RAID 1(미러링)**을 직접 구축하고, 의도적인 디스크 장애 상황 발생 시 데이터가 안전하게 보호되는지 검증한 뒤 신규 디스크로 복구(Rebuilding)하는 전 과정입니다.

---

## 🛠️ 1. 디스크 준비 및 파티션 설정
* 추가된 가상 디스크(`/dev/sdb`, `/dev/sdc`)에 파티션을 생성하고 `Linux raid autodetect` 타입으로 설정합니다.
* 디스크 인식 상태를 확인합니다.
  ![디스크 생성 및 파티션 확인](ch.6%20디스크%20관리와%20공간%20할당/raid1/1.create_2_disk&partition.png)

---

## 2. RAID 1 배열 생성 및 마운트
* `mdadm` 명령어를 통해 두 파티션을 묶어 RAID 1(`/dev/md1`) 배열을 생성합니다.
* 파일 시스템(ext4)을 포맷한 후, `/raid1` 디렉토리에 마운트하여 정상 작동을 확인합니다.
  ![RAID 1 생성 및 마운트](ch.6%20디스크%20관리와%20공간%20할당/raid1/2.raid_mount.png)

---

## 3. RAID 상태 상세 점검
* `mdadm --detail /dev/md1` 명령어로 현재 배열 상태(`State : clean`), 활성화된 디스크 개수(`Active Devices : 2`), 그리고 미러링 구성 상태를 정밀 진단합니다.
  ![RAID 상세 상태 확인](ch.6%20디스크%20관리와%20공간%20할당/raid1/3.reboot&check_status.png)

---

## 4. 테스트 파일 생성 (데이터 적재)
* 마운트된 `/raid1` 경로에 `testFile`을 생성하여, 미러링 환경 내에 정상적으로 데이터가 저장되는지 확인합니다.
  ![테스트 파일 생성](ch.6%20디스크%20관리와%20공간%20할당/raid1/4.insert_testfile_as_data.png)

---

## 5. 🚨 디스크 장애 시뮬레이션 (Fault Injection)
* RAID의 핵심인 안정성 검증을 위해, 구성 디스크 중 하나를 강제로 장애 상태(`faulty`)로 만들거나 제거하여 **Degraded(저하)** 상태로 진입시킵니다.
* 디스크 1개가 유실되었음에도 시스템과 데이터가 유지되는지 확인합니다.
  ![디스크 장애 발생 상태](ch.6%20디스크%20관리와%20공간%20할당/raid1/5.problem_status.png)

---

## 6. 🔄 디스크 교체 및 복구 (Recovery & Rebuilding)
* 시스템 환경에서 디스크 상태를 점검(`lsblk -S` 등)합니다.
  ![디스크 상태 점검](ch.6%20디스크%20관리와%20공간%20할당/raid1/6.%20solve_add_disk.png)

* 장애가 난 자리에 정상 디스크를 다시 추가(`--add`)하여 패리티 동기화 및 복구 작업을 수행합니다.
  ![디스크 추가 및 복구](ch.6%20디스크%20관리와%20공간%20할당/raid1/7.%20add_disk_inRaid.png)

---

## 7. ✅ 데이터 무결성 최종 검증
* 복구 완료 후 `mdadm --detail /dev/md1`을 통해 상태가 `clean`으로 돌아온 것을 확인합니다.
* `/raid1` 경로에 들어가 장애 발생 이전에 생성했던 `testFile`이 손실 없이 그대로 남아있는지 최종 확인합니다.
  ![데이터 최종 검증](ch.6%20디스크%20관리와%20공간%20할당/raid1/8.%20check_data.png)




  # 🛡️ Linux Software RAID 5 (분산 패리티) 구축 및 장애 복구(Fault Tolerance & Recovery) 실습

가상머신 환경에서 3개 이상의 디스크를 활용하여 **RAID 5(분산 패리티)**를 직접 구축하고, 의도적인 디스크 장애 상황 발생 시 패리티 연산을 통해 데이터 무결성이 유지되는지 검증한 뒤 신규 디스크로 복구(Rebuilding)하는 전 과정입니다.

---

## 🛠️ 실습 환경 (Environment)
* **OS:** Rocky Linux
* **도구:** `mdadm`, `fdisk`, `mkfs.ext4`, `mount`, `df`
* **저장장치:** 추가 가상 디스크 (`sdb`, `sdc`, `sdd`)

---

## 🛠️ 1. 디스크 파티션 설정 및 인식 확인
* 추가된 가상 디스크들에 각각 파티션을 생성하고 `Linux raid autodetect` 타입으로 설정합니다.
* `ls -l /dev/sd*` 명령어를 통해 파티션(`sdb1`, `sdc1`, `sdd1`)이 정상적으로 생성되었는지 확인합니다.
  ![디스크 파티션 확인](ch.6%20디스크%20관리와%20공간%20할당/raid5/1.create_3disk&partition.png)

---

## 2. RAID 5 배열 생성 및 파일시스템 마운트
* `mdadm --create /dev/md5 --level=5 --raid-devices=3` 명령어로 3개의 파티션을 묶어 RAID 5 배열을 생성합니다.
* ext4 파일시스템으로 포맷(`mkfs.ext4`)한 뒤, `/raid5` 디렉토리에 마운트합니다.
  ![RAID 5 생성 및 마운트](ch.6%20디스크%20관리와%20공간%20할당/raid5/2.raid&mount.png)

---

## 3. RAID 5 배열 상세 상태 점검
* `mdadm --detail /dev/md5` 명령어로 RAID 레벨(`raid5`), 활성화된 장치 수(`Active Devices : 3`), 그리고 배열 상태(`State : clean`)를 정밀 진단합니다.
  ![RAID 5 상세 상태 확인](ch.6%20디스크%20관리와%20공간%20할당/raid5/3.check_raid5_byte.png)

---

## 4. 파일 시스템 용량 확인 및 테스트 파일 적재
* `df` 명령어로 마운트된 `/raid5`의 용량을 확인합니다.
* `/raid5` 경로 내부에 `testFile`을 생성하여 데이터가 정상적으로 적재되는지 검증합니다.
  ![용량 확인 및 테스트 파일 생성](ch.6%20디스크%20관리와%20공간%20할당/raid5/4.check_by_df&insert_data.png)

---

## 5. 🚨 디스크 장애 시뮬레이션 (Degraded 상태 진입)
* RAID 5 구성 디스크 중 하나를 제거(`--fail` 또는 가상 환경 내 디스크 유실 상황)하여 의도적인 장애를 발생시킵니다.
* `mdadm --detail /dev/md5`를 통해 배열 상태가 **`clean, degraded`**로 변경되고 워킹 디바이스가 2개로 줄어들었음에도 시스템이 유지되는지 확인합니다.
  ![디스크 장애 발생 및 저하 상태](ch.6%20디스크%20관리와%20공간%20할당/raid5/5.situation_delete_disk_status.png)

---

## 6. 🔄 디스크 상태 점검 및 복구 준비
* 시스템의 디스크 및 파티션 연결 상태를 점검하기 위해 `lsblk -S` 및 `ls -l /dev/sd*`를 실행하여 교체/추가할 디스크 환경을 파악합니다.
  ![디스크 상태 점검](ch.6%20디스크%20관리와%20공간%20할당/raid5/6.solve_add_disk.png)

---

## 7. ✅ 패리티 동기화 및 최종 복구 검증
* 장애가 난 자리에 정상 디스크 파티션(`/dev/sdb1`)을 다시 추가(`--add`)하여 리빌딩 작업을 수행합니다.
* `mdadm --detail /dev/md5`를 통해 상태가 다시 **`clean`**으로 복구되었고 Active Devices가 3개로 정상 회복되었는지 최종 확인합니다.
  ![디스크 추가 및 복구 완료](ch.6%20디스크%20관리와%20공간%20할당/raid5/7.add_raid.png)