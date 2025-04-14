# 오토스케일링

Kubernetes에서는 리소스 상황에 따라 **자동으로 리소스를 확장하거나 축소**할 수 있는 다양한 오토스케일링 기능을 제공합니다.

---

## ✅ 주요 오토스케일링 구성 요소



---

### 🔹 HPA (Horizontal Pod Autoscaler)  
---
### 🔹 Karpenter  
노드 수준의 오토스케일링을 담당하며, Pod가 스케줄되지 못하고 Pending 상태일 때 **자동으로 새로운 Worker Node를 생성**합니다.

![karpenter](https://github.com/user-attachments/assets/ef9bb4fd-2d13-46c3-b0bf-198991b001bd)

#### ⚙️ 작동 흐름

1. **HPA가 Pod를 자동 확장** 또는 재배포합니다.
2. **Kube-scheduler**가 기존 Node에 Pod를 스케줄링하려 시도합니다.
3. 기존 Node에 자원이 부족해 Pod는 **Pending 상태**가 됩니다.
4. **Karpenter가 Pending 상태의 Pod를 감지**하고, 새로운 Worker Node를 **자동 생성**합니다.
5. **Kube-scheduler가 새 Node에 Pod를 스케줄링**합니다.

#### ✅ 장점

- 노드 프로비저닝과 Pod 스케줄링을 자동으로 처리
- 스케일링 속도가 빠름
- Pod 요구사항에 최적화된 노드 생성
- 불필요한 노드 종료로 **비용 절감 효과**
  
---

# 무중단 배포  
## 블루/그린 배포 (Blue-Green Deployment)

두 개의 동일한 환경(**블루**, **그린**)을 준비하여  
한쪽 환경에서 서비스를 운영하는 동안 다른 환경에 새 버전을 배포합니다.  
보통 **blue가 현재 운영 중**, **green이 새 버전**을 의미합니다.<br>
![65c3a844357606c1b08a21f0_Screen-Shot-2021-03-19-at-2 34 46-PM](https://github.com/user-attachments/assets/0ef46c75-a9de-418a-abdf-770a1e03285b)

---

## ⚙️ 배포 흐름

1. **그린 환경 구성**  
   - 현재 운영 중인 블루 환경과 동일한 사양으로 그린 환경을 준비합니다.
2. **새로운 버전 배포**  
   - 그린 환경에 새로운 버전의 애플리케이션을 배포합니다.
3. **테스트 및 검증**  
   - 그린 환경에서 애플리케이션을 테스트하고 검증합니다.
4. **트래픽 전환**  
   - 테스트 완료되면 로드 밸런서를 통해 트래픽을 블루에서 그린으로 전환합니다.
5. **모니터링**  
   - 그린 환경에서 애플리케이션이 정상적으로 **운영되는지 모니터링**합니다.

---

## ✅ 장점

- **실제 운영 환경 테스트**: 새로운 버전을 운영 환경과 동일한 환경에서 사전 테스트 가능합니다.  
- **빠른 롤백**: 문제가 생기면 트래픽을 간단히 **블루로 되돌려** 빠르게 복구 가능합니다.

---

## ⚠️ 단점

- **비용**: 블루와 그린 환경을 **동시에 유지**해야 하므로 자원이 2배 필요합니다.

---

## 🔁 그린 → 블루 롤백 명령어

```bash
kubectl argo rollouts undo rollout rollout-bluegreen -n bluegreen-test
```




## 🧭 ArgoCD 사용 이유

> **지속적 배포(CD)**를 안정적이고 자동화된 방식으로 구현하기 위해  
> GitOps 방식의 도구인 **ArgoCD**를 선택했습니다.

ArgoCD는 Git 저장소를 애플리케이션의 단일 진실 소스로 삼아  
변경 사항을 자동으로 감지하고 배포하는 **GitOps 기반 CD 도구**입니다.

---



## 🧭 ArgoCD 사용 이유

> **지속적 배포(CD)**를 안정적이고 자동화된 방식으로 구현하기 위해  
> GitOps 방식의 도구인 **ArgoCD**를 선택했습니다.

ArgoCD는 Git 저장소를 애플리케이션의 단일 진실 소스로 삼아  
변경 사항을 자동으로 감지하고 배포하는 **GitOps 기반 CD 도구**입니다.

---

## ✅ ArgoCD의 주요 장점

<details>
<summary><strong>1. Git 중심의 배포 관리</strong></summary>

Argo CD는 Git에 기록된 설정을 기준으로 애플리케이션을 배포합니다.  
모든 변경 내용은 Git에 남기 때문에 **버전 관리가 쉽고**, 문제가 생기면 이전 상태로 **쉽게 되돌릴 수 있습니다.**

</details>

<details>
<summary><strong>2. 자동화된 동기화</strong></summary>

Argo CD는 GitOps 원칙에 따라 **자동으로 클러스터와 동기화**합니다.  
Git에 변경 사항이 생기면 이를 감지해 Kubernetes를 자동으로 업데이트합니다.  
덕분에 배포를 수동으로 할 필요가 없고, **안정적인 운영이 가능합니다.**

</details>

<details>
<summary><strong>3. Blue-Green 배포 전략 지원</strong></summary>

Argo CD는 **Blue-Green 배포 전략**을 지원합니다.  
운영 중인 환경(Blue)과 새 버전 환경(Green)을 동시에 유지하고, 문제 없는 것을 확인한 후 트래픽을 Green으로 전환합니다.  
**무중단 배포와 빠른 롤백**이 가능합니다.

</details>

<details>
<summary><strong>4. 확장성과 유연성</strong></summary>

Argo CD는 대규모 Kubernetes 클러스터에서도 **유연하게 확장**할 수 있습니다.  
여러 클러스터를 동시에 관리할 수 있으며, 클러스터마다 **다른 설정을 적용**할 수 있습니다.  
덕분에 다양한 환경에서도 **일관된 배포가 가능합니다.**

</details>

<details>
<summary><strong>5. 강력한 보안성</strong></summary>

Argo CD는 Git을 기준으로 애플리케이션 상태를 관리하므로, **누가 언제 무엇을 배포했는지 추적**할 수 있습니다.  
배포 실수나 보안 사고를 사전에 방지할 수 있고, **인증과 권한 관리**도 지원하여 팀 단위 보안 운영이 가능합니다.

</details>


![image](https://github.com/user-attachments/assets/67c71765-abf2-4703-94fe-3cba6a0b1e6a) ![image](https://github.com/user-attachments/assets/fc41b3c7-2094-4735-ae7f-faba9ab045d7)



