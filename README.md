# Monitoring Kubernetes Cluster dengan Prometheus dan Grafana

<br>Dibutuhkan Helm dan Tiller, lakukan inisialisasi tiller dan repo helm, dengan menggunakan perintah:</br>
<br>$ kubectl apply -f helm/01-serviceaccount.yaml</br>
<br>$ kubectl apply -f helm/02-rolebinding.yaml
<br>$ helm init --service-account tiller --wait
<br>$ helm repo update
<br>
<br>Langkah pertama adalah create namespace, dengan menggunakan perintah:
<br>$ kubectl apply -f 01-namespace.yaml
<br>
<br>Kemudian dilanjutkan dengan nstalasi prometheus menggunakan helm chart, dengan perintah:
<br>$ helm install stable/prometheus --namespace bp-mon --name prometheus
<br>
<br>Langkah berikutnya adalah deploy configmap untuk grafana, dengan eprintah:
<br>$ kubectl apply -f 02-config.yaml
<br>
<br>Kemudian adalah instalasi grafana menggunakan helm chart dan values.yaml, dengan perintah:
<br>$ helm install stable/grafana -f 03-values.yaml --namespace bp-mon --name grafana
<br>
<br>Untuk mengakses grafana, lakukan beberapa perintah sebagai berikut:
<br>
<br>Mendapatkan password login UI grafana:
<br>$ kubectl get secret --namespace bp-mon grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
<br>
<br>Export pod name untuk kebutuhan port forward:
<br>$ export POD_NAME=$(kubectl get pods --namespace bp-mon -l "app=grafana,release=grafana" -o jsonpath="{.items[0].metadata.name}")
<br>
<br>Dan lakukan port forward service grafana dan akses dengan http://localhost:3000
<br>$ kubectl --namespace bp-mon port-forward --address 0.0.0.0 $POD_NAME 3000
<br>
<br>Untuk menghapus deployment prometheus dan grafana, dengan menggunakan perintah:
<br>$ helm del --purge prometheus
<br>$ helm del --purge grafana
<br>$ kubectl delete namespace -n bp-mon
