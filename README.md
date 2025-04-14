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
# 오토스케일링

Kubernetes에서는 리소스 상황에 따라 **자동으로 리소스를 확장하거나 축소**할 수 있는 다양한 오토스케일링 기능을 제공합니다.

<details>
<summary><h2>Karpenter</h2></summary>
   
![image](https://github.com/user-attachments/assets/48cb262b-7f8e-4f68-8af7-5ebf8b7aff9e)


### 1. 노드 생성

Pod를 스케줄링할 수 있는 노드가 없어서 `Pending` 이벤트가 발생하면, Karpenter는 이를 감지해 **새로운 노드를 자동 생성**합니다.

### 2. 노드 삭제

- DaemonSet 제외한 Pod가 없을 때
- 노드 유지 시간이 지났을 때
- 노드를 한 개로 합칠 수 있을 때

### 3. 스케쥴링

Karpenter는 노드 생성/삭제 시 **직접 Pod를 스케쥴링**합니다.

</details>

<details>
<summary><h2>HPA (Horizontal Pod Autoscaler)</h2></summary>

✍️ 내용은 추후 작성 예정입니다.

</details>
