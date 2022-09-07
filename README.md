# DevOps-Bootcamp
DevOps Bootcamp' i sonunda verilen proje ödevi


1-GCP’de Kubernetes cluster kurulumu:

İlk önce Google Cloud Platformu’ nda bir kullanıcı girişi yapmamız gerekiyor. Kullanıcı girişi yaptıktan sonra karşımıza welcome sayfası gelecektir. Bu sayfa karşımıza bir proje oluşturarak geliyor, muhtemelen adı ‘My First Project’ olacaktır. Projenin numarası, ID’ si ile beraber geliyor. Yeni bir proje oluşturmayı seçebilir veya bu projeden devam edebilirsiniz. 

![qqq](https://user-images.githubusercontent.com/95285650/188975913-f27fd64b-b1c3-43db-8cff-ba04cf06c697.png)

Sonraki adımımız sayfanın sol üst köşesinde yer alan üç çizgiye tıklamak ve ardından ‘Kubernetes Engine’ sekmesinde ‘Clusters’ maddesine tıklamak olacak.

![2](https://user-images.githubusercontent.com/95285650/188976352-db664b82-03e1-4ca5-9125-3e001f401feb.png)

Karşımıza çıkan bu sayfada Kubernetes Engine Cluster’ ı kullanabilmemiz için etkinleştirmemiz gerekiyor. Bu nedenle ‘Enable’ sekmesine tıklamalıyız. En fazla birkaç dakika bekledikten sonra etkinleştirme işlemimiz başarıyla tamamlanıyor ve ardından bu sayfa karşımıza geliyor. Buradan create e basıyoruz. 

![3](https://user-images.githubusercontent.com/95285650/188976589-e753c50c-cb45-44a0-bbbc-1223c96bc1d2.png)

Standart modu seçiyoruz çünkü bu bize daha fazla kontrol imkanı veriyor.

![4](https://user-images.githubusercontent.com/95285650/188976748-ca8c5fe4-bc1e-4c27-a628-55c2b5bc4809.png)

Clusterımızın basic tanımlamalarını yapıyoruz. Location Type contol plane ve node larin olacağı yer burası. Location type olarak regional seçeneğini işaretliyoruz çünkü zonal seçeneği tek bir yerde açarken regional 3 yere dağıtıyor. Bunun önemli olmasının sebebi zonal seçeneği tek bir yerden açıyor ve bu hata alırsa sorun yaşamış oluyoruz. Regional seçeneği ise 3 data center açıyor eğer birinde sorun olursa diğer data centerları kullanabiliriz. Bu da bize avantaj sağlıyor.

![5](https://user-images.githubusercontent.com/95285650/188977217-055191a9-65a9-4723-87f3-2251b4f12a85.png)

Node poollara baktığımızda bunlar aslında vm lerdir. Size data center(zone) başına kaç vm(makine)kullanacağını söyler. Enable Cluster Autoscaler ihtiyaç halinde otomatik yeni makineler yüklüyor. Specify Node Locations zonelara özel nodelar açıyor. Biz 3 zone için istediğimizden bu seçeneği işaretlemiyoruz.

![6](https://user-images.githubusercontent.com/95285650/188977777-4649472b-1b94-430e-b2d9-93a26661b728.png)

Nodes lara baktığımızda image type seçiyoruz. Boot diskise imajlarımızın ne kadar yer kaplayacağını belirletiyor. 100GB bizim için yeterli olacak.

![7](https://user-images.githubusercontent.com/95285650/188978194-025b7908-be23-4225-b095-427992c46b28.png)

Networking sekmesine geldiğimizde node başına kaç pod geleceğini belirtebiliyoruz. Network Tags ile firewall ları belirliyoruz.

![8](https://user-images.githubusercontent.com/95285650/188978534-4c7b9874-295f-4aa3-ae43-4a36ce9f9e30.png)

Create e basarak cluster işlemimizi tamamlamış oluyoruz. Bu clusterı oluşturmak için bir başka yöntem daha var ve bu yöntemde terraform kullanmak. Ayrıca terraformla oluşturduğumuz cluster bize ekstra olarak default nodunu kapatıp yerine detaylarını verdiğimiz node umuzu oluşturuyor. 

TERRAFORM İLE GKE OLUŞTURMA:

Desktop ta bootcamp adlı bir dosya oluşturduk. Vscode da bu dosyayı açtık ve sağ tıklayarak yeni bir file oluşturuyoruz. File ismi main.tf -.tf uzantılı olması terraform kodlu olacağı anlamına geliyor- . Bu dosyamızı şu şekilde yazıyoruz.

![9](https://user-images.githubusercontent.com/95285650/188979213-32f2ad7c-bce5-41e6-becd-8415b6dd2b6b.png)

![10](https://user-images.githubusercontent.com/95285650/188979359-146e2ec3-ea98-4159-bb3e-8e67e3d7860e.png)

=>Provider, projenin adı, bölge ve zone seçiyoruz. Bize en yakın olan bölge Frankfurt olduğundan europe-west3 seçtik. 

=>Compute instanse ismi bootcamp olarak belirledik, makine tipi de e2-micro seçtik-disk imajımızı ubuntu 20.04 kullandığımdan bunu seçtim-. 

=>Network interface oluşturduk.

=>Networkü google_compute_network satırında oluşturduk. Subnetleri otomatik oluşturdu. Böylelikle sanal makine oluşturdu içinde subnetler otomatik olacak ve içinde vm açacak.

=>Boot disk satırında vm (sanal makine) açıyor-ubuntu olarak belirledik-.

Bunları tamamladıktan sonra vscod terminalinde:

-terraform plan –out plan.out 

Kodunu yazıyoruz, kendi oluşturduğu planı getiriyor ve bu planı uygulamaya döküyor. 

-terraform apply "plan.out" 

Bu kodu yazdığımızda ise planı uygulatmış oluyoruz.


2. Kubernetes üzerine MySQL kurulumu + doküman hazırlanması

Aşağıdaki kodu oluşturduğumuz main.tf dosyamıza yazıyoruz. Bu kod Google Cloud' un desteklediği Cloud Sql veritabanını temsil eder.

resource "google_sql_database" "database" {
  name     = "my-database"
  instance = google_sql_database_instance.instance.name
}

resource "google_sql_database_instance" "instance" {
  name             = "my-database-instance"
  region           = "europe-west3"
  database_version = "MYSQL_8_0"
  settings {
    tier = "db-f1-micro"
  }

  deletion_protection  = "true"
}

Kaydettikten sonra vscode terminalinde 

-terraform apply 

yazıyoruz ve Sql veri tabanını Google Cloud projemize aktarmasını bekliyoruz. 

![11](https://user-images.githubusercontent.com/95285650/188982353-87ad78b1-d12b-4d41-9568-fa94a6412c81.png)

Cloud sayfamıza gidiyoruz. Api leri etkinleştirmemiz gerekiyor : 
--Compute Engine API
--Cloud SQL Admin API
--Google Kubernetes Engine API
--Artifact Registry API
--Cloud Build API

SQL Instance sayfasından örnek oluşturuyoruz, MYSQL'i tıklıyoruz kimlik olarak bootcamp-mysql girdik parola olarak 123456 yazdık ve bölge seçtik konfigürasyonlarda makine türünü işaretliyoruz ve oluştur seçeneğine tıklıyoruz. 

![12](https://user-images.githubusercontent.com/95285650/188983400-5f9c17a6-85af-47ad-a36c-57da277d6085.png)

Veritablanları sekmesinden veritabanı oluşturuyouz bootcamp-database isminde. Kullanıcı oluşturduk ekleye bastık.

Kullanıcı hesabı ekliyoruz kullanıcı adını yazdık, şifreyi girdik ip eklemeden oluşturuyoruz çünkü ip adresini otomatik olarak verecek.

Shell e giderek şu kodu yazıyoruz:

-gcloud container clusters get-credentials bootcamp-gke \ --region europe-west3

Bu komut oluşturduğumuz cluster ı etkinleştirecektir.

![13](https://user-images.githubusercontent.com/95285650/188984269-b99c335e-24f5-41a6-a8e1-4d10bc4a0604.png)

Hizmet hesabı oluşturmak için:  

-gcloud iam service-accounts create bootcamp -service-account \
  --display-name="Bootcamp GKE Service Account"
 
komutunu girdik. Oluşturduğumuz hizmet hesabına client rolünü eklemek için bu komutu giriyoruz:

-cloud projects add-iam-policy-binding bootcamp-361405 \  
  --member="serviceAccount:bootcamp-service-account@bootcamp-361405.iam.gserviceaccount.com" \  
  --role="roles/cloudsql.client"
  --member="serviceAccount:bootcamp-serviceaccount@bootcamp-361405.iam.gserviceaccount.com" \
  --role="roles/cloudsql.client"
  
![14](https://user-images.githubusercontent.com/95285650/188984699-357d546b-7d6e-4e76-adc1-1a2ac90d1e2f.png)

Role ve binding izinlerini vermemiz gerekiyor. Role bir kaynağa hangi erişimi verilmesi gerektiğini belirtiyor. Role ve policy bindingler aşağıdaki komutlarla oluşturuldu.

-gcloud iam service-accounts add-iam-policy-binding \
  --role="roles/iam.workloadIdentityUser" \
  --member="serviceAccount:bootcamp-361405.svc.id.goog[default/ksa-cloud-sql]" \
  bootcamp-serviceaccount@bootcamp-361405.iam.gserviceaccount.com

Service account güncellemesi yapıyoruz.

-kubectl annotate serviceaccount \
  ksa-cloud-sql  \
  iam.gke.io/bootcamp-service-account=bootcamp-serviceaccount@bootcamp-361405.iam.gserviceaccount.com
  
Secretları oluşturduk:
  
-kubectl create secret generic gke-cloud-sql-secrets \
  --from-literal=database=bootcamp-database \
  --from-literal=username=sqlserver \
  --from-literal=password=123456
  
 ![15](https://user-images.githubusercontent.com/95285650/188986239-a30cb0ab-a33a-4f9c-9c3f-54854962dec8.png)
 
Artifact repomuzu oluşturuyoruz:

-gcloud artifacts repositories create gke-cloud-sql-repo \
  --project=bootcamp-361405 \
  --repository-format=docker \
  --location=europe-west3 \
  --description="GKE Bootcamp sample app"

Docker file kullanarak bir derleme yapmasını istiyoruz:

-gcloud builds submit \
  --tag europe-west3-docker.pkg.dev/bootcamp-361405/gke-cloud-sql-repo/gke-sql .

Çıktımız şu şekilde:

Output:
ID: 46bc9ba8-d14a-4ae8-b295-74b405d3cc39
CREATE_TIME: 2022-09-06T11:20:53+00:00
DURATION: 2M2S
SOURCE: gs://bootcamp-361405_cloudbuild/source/1662463251.368372-d644a3b9b13f49c4a4f5dcdd6bb1fd16.tgz
IMAGES: europe-west3-docker.pkg.dev/bootcamp-361405/gke-cloud-sql-repo/gke-sql (+1 more)
STATUS: SUCCESS

SQL instance açıklamasını almak için aşağıdaki komutu çalıştırıyoruz:

-gcloud sql instances describe bootcamp-instance --format='value(connectionName)' 

Artık servisleri incelediğimizde aşağıdaki sonuçları alıyoruz:

-kubectl get services

NAME                TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
gke-cloud-sql-app   LoadBalancer   10.3.240.188   <pending>     80:30154/TCP   10s

3. Kubernetes üzerine WordPress kurulumu + otomatik ayarlar









  
  
  
  
  
  
  
  
  
  
  
 
 
 


