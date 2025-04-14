# 오토스케일링

Kubernetes에서는 리소스 상황에 따라 **자동으로 리소스를 확장하거나 축소**할 수 있는 다양한 오토스케일링 기능을 제공합니다.

---

## ✅ 주요 오토스케일링 구성 요소

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

### 🔹 HPA (Horizontal Pod Autoscaler)  
리소스 사용량(CPU, 메모리 등)에 따라 **Pod 개수를 수평적으로 자동 조절**합니다.

> 예: CPU 사용률이 70%를 넘으면 Pod 수를 증가시킴

#### ⚙️ 작동 흐름

1. HPA는 설정된 리소스 기준(CPU, 메모리)을 지속적으로 모니터링합니다.
2. 사용량이 기준을 초과하거나 미달하면 **Replica 수를 자동 조절**합니다.
3. Deployment 또는 ReplicaSet의 `replicas` 수를 변경해 Pod 수를 늘리거나 줄입니다.

#### ✅ 장점

- 트래픽 급증 시 자동으로 확장하여 안정성 유지
- 리소스 낭비 없이 효율적인 운영 가능
- 다양한 메트릭(Custom Metrics 포함)을 기반으로 동작

---

## 💡 HPA + Karpenter 조합

- HPA는 Pod 수를 조절  
- Karpenter는 Pod를 수용할 수 있도록 **노드를 자동 생성/삭제**  
→ **Pod와 Node 모두 유연하게 확장/축소 가능**

---

## ✨ 예시 명령어

### HPA 생성

```bash
kubectl autoscale deployment my-app --cpu-percent=50 --min=2 --max=10
```

# 무중단 배포  
## 블루/그린 배포 (Blue-Green Deployment)

두 개의 동일한 환경(**블루**, **그린**)을 준비하여  
한쪽 환경에서 서비스를 운영하는 동안 다른 환경에 새 버전을 배포합니다.  
보통 **blue가 현재 운영 중**, **green이 새 버전**을 의미합니다.<br>
![65c3a844357606c1b08a21f0_Screen-Shot-2021-03-19-at-2 34 46-PM](https://github.com/user-attachments/assets/0ef46c75-a9de-418a-abdf-770a1e03285b)

---

## ✅ 배포 흐름

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

