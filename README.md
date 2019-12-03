# Monitoring Kubernetes Cluster dengan Prometheus dan Grafana

Dibutuhkan Helm dan Tiller, lakukan inisialisasi tiller dan repo helm, dengan menggunakan perintah:
$ kubectl apply -f helm/01-serviceaccount.yaml
$ kubectl apply -f helm/02-rolebinding.yaml
$ helm init --service-account tiller --wait
$ helm repo update

Langkah pertama adalah create namespace, dengan menggunakan perintah:
$ kubectl apply -f 01-namespace.yaml

Kemudian dilanjutkan dengan nstalasi prometheus menggunakan helm chart, dengan perintah:
$ helm install stable/prometheus --namespace bp-mon --name prometheus

Langkah berikutnya adalah deploy configmap untuk grafana, dengan eprintah:
$ kubectl apply -f 02-config.yaml

Kemudian adalah instalasi grafana menggunakan helm chart dan values.yaml, dengan perintah:
$ helm install stable/grafana -f 03-values.yaml --namespace bp-mon --name grafana

Untuk mengakses grafana, lakukan beberapa perintah sebagai berikut:

Mendapatkan password login UI grafana:
$ kubectl get secret --namespace bp-mon grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo

Export pod name untuk kebutuhan port forward:
$ export POD_NAME=$(kubectl get pods --namespace bp-mon -l "app=grafana,release=grafana" -o jsonpath="{.items[0].metadata.name}")

Dan lakukan port forward service grafana dan akses dengan http://localhost:3000
$ kubectl --namespace bp-mon port-forward --address 0.0.0.0 $POD_NAME 3000

Untuk menghapus deployment prometheus dan grafana, dengan menggunakan perintah:
$ helm del --purge prometheus
$ helm del --purge grafana
$ kubectl delete namespace -n bp-mon
