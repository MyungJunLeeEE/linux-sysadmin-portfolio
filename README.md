# 🐧 Linux System Administration Practical Portfolio

Rocky Linux 환경에서 진행한 스토리지 관리(RAID, LVM, Quota) 및 시스템 관리 실습을 정리한 포트폴리오 저장소입니다.

---

## 🛠️ 실습 환경 (Environment)
* **OS:** Rocky Linux 9 (or CentOS)
* **Hypervisor:** VMware Workstation
* **Storage Management Tools:** `mdadm`, `lvm2` (`pvcreate`, `vgcreate`, `lvcreate`), `quota`

---

## 📌 주요 실습 목차 (Table of Contents)

### 1. 🛡️ Software RAID 구축 및 장애 복구 (Software RAID 1 & 5)
* **주요 내용:** 
  * `mdadm`을 활용한 Software RAID 1 (Mirroring) 및 RAID 5 (Distributed Parity) 구축
  * 의도적 디스크 장애 발생 시 패리티/미러 기반 데이터 무결성 및 연속 가용성 검증
  * 결함 디스크 강제 이탈, 신규 디스크 추가 및 백그라운드 자동 리빌딩(Rebuilding)
* **상세 보고서:**
  * 🔴 [RAID 1 (Mirroring) 구축 및 복구 실습](./SW_RAID_1&5/raid1/README.md)
  * 🟡 [RAID 5 (Distributed Parity) 구축 및 복구 실습](./SW_RAID_1&5/raid5/README.md)

---

### 2. 📦 [LVM (Logical Volume Manager) 유연한 스토리지 관리](./LVM/README.md)
* **주요 내용:**
  * 물리 디스크 파티셔닝 및 PV(Physical Volume), VG(Volume Group), LV(Logical Volume) 생성
  * 파일시스템 마운트 및 `/etc/fstab`을 통한 영구 마운트 설정
* **상세 보러가기:** [`LVM/README.md`](./LVM/README.md)

---

### 3. 🔒 [Disk Quota 디스크 사용자/그룹 용량 제한](./quota/README.md)
* **주요 내용:**
  * `usrquota`, `grpquota` 마운트 옵션을 통한 파일시스템 쿼터 활성화
  * `quotacheck`, `edquota`를 이용한 사용자별 Soft/Hard Limit 용량 제한 적용 및 검증
* **상세 보러가기:** [`quota/README.md`](./quota/README.md)

---

## 📂 저장소 디렉토리 구조

```text
linux-sysadmin-portfolio/
│
├── LVM/                        # LVM 실습 폴더
│   ├── 1.add_2_disk&patition.png
│   ├── 2.pvcreate&vg.png
│   ├── 3.lvcreate.png
│   ├── 4.mount&df.png
│   └── README.md
│
├── SW_RAID_1&5/                # RAID 1 & 5 실습 폴더
│   ├── raid1/                  # RAID 1 상세 실습 보고서 및 이미지
│   └── raid5/                  # RAID 5 상세 실습 보고서 및 이미지
│
├── quota/                      # Disk Quota 실습 폴더
│   ├── 1.add_1disk&partition.png
│   ├── ...
│   └── README.md
│
└── README.md                   # 메인 대표 README (현재 파일)