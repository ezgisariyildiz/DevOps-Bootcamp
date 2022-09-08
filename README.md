# DevOps-Bootcamp
DevOps Bootcamp' i sonunda verilen proje ödevi


# 1-GCP’de Kubernetes cluster kurulumu:

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

Nodes lara baktığımızda image type seçiyoruz. Boot diskise imajlarımızın ne kadar yer kaplayacağını belirletiyor.

![7](https://user-images.githubusercontent.com/95285650/188978194-025b7908-be23-4225-b095-427992c46b28.png)

Networking sekmesine geldiğimizde node başına kaç pod geleceğini belirtebiliyoruz. Network Tags ile firewall ları belirliyoruz.

![8](https://user-images.githubusercontent.com/95285650/188978534-4c7b9874-295f-4aa3-ae43-4a36ce9f9e30.png)

Create e basarak cluster işlemimizi tamamlamış oluyoruz. Bu clusterı oluşturmak için bir başka yöntem daha var ve bu yöntemde terraform kullanmak. Ayrıca terraformla oluşturduğumuz cluster bize ekstra olarak default nodunu kapatıp yerine detaylarını verdiğimiz node umuzu oluşturuyor.

# TERRAFORM İLE GKE OLUŞTURMA:

İlk olarak vscode üzerinden bir dosya oluşturuyoruz ve bu dosyanın içerisine bir main.tf adında file ekliyoruz-.tf uzantılı olması terraform kodlu olacağı anlamına geliyor-. Terminalde:

-brew install --cask google-cloud-sdk

komutu ile google cloud sdk sını kurmuş oluyoruz.

-gcloud init

google cloud başlatılmış oluyor.

-gcloud auth application-default login

şu kodu çalıştırarak APİ erişimi için kendi kullanıcımızı tanımlamış oluyoruz.

-gcloud config set project PROJECT_ID
-gcloud config set compute/zone COMPUTE_ZONE
-gcloud config set compute/region COMPUTE_REGION

Daha önceden tanımladığımız proje id, zone ve region bilgilerini yazarak projemize bağlanıyoruz.

Oluşturduğumuz main.tf dosyamızı şu şekilde yazıyoruz:

![9](https://user-images.githubusercontent.com/95285650/188979213-32f2ad7c-bce5-41e6-becd-8415b6dd2b6b.png)

![10](https://user-images.githubusercontent.com/95285650/188979359-146e2ec3-ea98-4159-bb3e-8e67e3d7860e.png)

=>Provider, projenin adı, bölge ve zone seçiyoruz. Bize en yakın olan bölge Frankfurt olduğundan europe-west3 seçtik. 

=>Compute instanse ismi bootcamp olarak belirledik, makine tipi de e2-micro seçtik-disk imajımızı ubuntu 20.04 kullandığımdan bunu seçtim-. 

=>Network interface oluşturduk.

=>Networkü google_compute_network satırında oluşturduk. Subnetleri otomatik oluşturdu. Böylelikle sanal makine oluşturdu içinde subnetler otomatik olacak ve içinde vm açacak.

=>Boot disk satırında vm (sanal makine) açıyor-ubuntu olarak belirledik-.

Bunları tamamladıktan sonra vscod terminalinde:

-terraform plan –out plan.out 

Kodunu yazıyoruz, kendi oluşturduğu planı getiriyor ve onaylamamız durumunda bu planı uygulayacağını söylüyor. 

-terraform apply "plan.out" 

Bu kodu yazdığımızda ise planı uygulatmış oluyoruz.


# 2. Kubernetes üzerine MySQL kurulumu + doküman hazırlanması

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

Kaydettikten sonra vscode terminalinde:

-terraform apply 

yazıyoruz ve sql veri tabanını Google Cloud projemize aktarmasını bekliyoruz. 

![11](https://user-images.githubusercontent.com/95285650/188982353-87ad78b1-d12b-4d41-9568-fa94a6412c81.png)

Cloud sayfamıza gidiyoruz. Api leri etkinleştirmemiz gerekiyor : 
--Compute Engine API
--Cloud SQL Admin API
--Google Kubernetes Engine API
--Artifact Registry API
--Cloud Build API

SQL Instance sayfasından örnek oluşturuyoruz, MYSQL'i tıklıyoruz kimlik olarak bootcamp-mysql girdik parola olarak 123456 yazdık ve bölge seçtik konfigürasyonlarda makine türünü seçiyoruz ve oluştur seçeneğine tıklıyoruz. 

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

Role ve binding izinlerini vermemiz gerekiyor. Rol, bir izin listesidir; her rol, önceden tanımlanmış bir IAM rolü veya kullanıcı tarafından oluşturulan özel bir rol olabilir. Bindings ise bir veya daha fazla üyeyi veya müdürü tek bir role bağlar. 

Role ve policy bindingler aşağıdaki komutlarla oluşturuldu.

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


# 3. Kubernetes üzerine WordPress kurulumu + otomatik ayarlar

MYSQL üzerine Wordpress kurulumu ayarları yapıyoruz:

storage.yaml oluşturduk kaydettik. Bu işlemden sonra sistem PV( Persistent Volume) ve PVC( Persistent Volume Claim) işlemlerini onaylar. 
Persistent Volume, kalıcı verilerimizi depolayan ana makinedeki fiziksel bir birimdir. 
Persistent Volume Claim ise kalıcı verilerimiz depolamak yani PV oluşturmak için yaptığımız taleptir. 
 
![16](https://user-images.githubusercontent.com/95285650/189094646-90a8ce83-0054-47e4-a1cb-c461f8948c53.png)

![17](https://user-images.githubusercontent.com/95285650/189094745-7ea5ecdb-3c22-4f80-a245-b1708d34ac28.png)

mysql-deployment.yaml oluşturduk kaydettik. deployment.yaml dosyası MySQL dağıtımının kullanacağı kaynakları tanımlar.

![18](https://user-images.githubusercontent.com/95285650/189094891-5665a417-60ff-4104-a2dc-543ac167bbb1.png)

![19](https://user-images.githubusercontent.com/95285650/189095086-aeee0ba7-4279-4af9-8dd3-1f784bae2af8.png)

wordpress-deployment.yaml oluşturduk kaydettik.

![1](https://user-images.githubusercontent.com/95285650/189095228-a81a2d28-ffc2-4335-8c8a-9e46bf621f63.png)

![2](https://user-images.githubusercontent.com/95285650/189095425-256ea39e-9948-49d4-b1ba-6c48072c19fa.png)

Podları inceliyoruz.

![3](https://user-images.githubusercontent.com/95285650/189095564-82e56202-fd58-42f4-b4ec-b55db84ea434.png)

Mysql kabuğuna erişebiliyoruz.

![4](https://user-images.githubusercontent.com/95285650/189095667-457ce1bd-b854-4df3-9f1a-ce17cfaad943.png)

Servisler ve podlar çalışıyor.

![5](https://user-images.githubusercontent.com/95285650/189095763-bcfa0701-b685-44f2-9ce1-f23699cc6ecb.png)


# 4. Ingress tanımı ile dışardan trafik alması için domain ayarı

Ingress, Kubernetes’ te çalıştırdığımız uygulamanın erişilmesi için dış trafiği Kubernetes servislerine bağlayan bir denetleyicidir.  Kubernetes Cluster’ ında oluşan network trafiğinin bir ayağı ingress dir ve ingress bir pod a, node a veya servise istek gelmesi olayıdır. Yani bir frontend uygulamasına dışarıdan gelen bir isteği Ingress olarak adlandırabiliriz. Kubernetes mimarisindeki ingress objesi ise tek bir Load Balancer üzerinden, http seviyesinde, kullanıcıya uygulamaya erişim imkanı sağlar. 
Bizde Wordpress ile yaptığımız uygulamaya dış trafikten bağlanmaya çalışıyoruz. Bunun için ingress ile çalışmamız gerekiyor. Öncelikle, kubernetes üzerinden oluşturduğumuz wordpress uyglamamız için global bir ip adresine ihtiyacımız var. Terminale aşağıdaki komutu yazıyoruz:

-gcloud compute addresses create kubernetes-ingress –global

Bu kod bize bir ingress ip si sağlayacaktır. Bu ip nin global olması önemli. İnternetten ulaşmamız için global olması gerekiyor. Outputta yer alan https adresi ingressle oluşturulmuş olup bu adresi url de aratabiliriz.

Daha sonra bir EOF(End of File) dosyası oluşturuyoruz. EOF dosyası bize komut ekranından ayrılmadan dosya açar. Açılan dosyaya yazmak istediğimiz bilgieri gidikten sonra tekrar EOF yazarak bu dosya içinden ayrılabiliriz ve tekrar komut satırına dönmüş oluruz.
Biz wordpress ingress için bir yaml dosyası açıyoruz. Bu dosyanın içerisinde kind, name, oluşturulan static ip nin adını giriyoruz ve dosyayı kaydedip çıkıyoruz.

![6](https://user-images.githubusercontent.com/95285650/189102615-e5b08e37-346f-4f5f-9818-65d1abe42236.png)

Ingress ip si için bir wordpress-ing-ip.yaml dosyası oluşturuyoruz ve ve bu dosyayı kaydediyoruz.

![7](https://user-images.githubusercontent.com/95285650/189102764-59d7b672-ddfb-4cb5-977a-d8914f9f046e.png)

Ingressin oluşturulup oluşturulmadığını öğrenmek için:

-kubectl get ingress wordpress

komutunu giriyoruz ve çıktımız şu şekilde:

![8](https://user-images.githubusercontent.com/95285650/189103058-f96c0c79-0a13-4bef-a2f1-5399faa08bdc.png)

Biraz bekledikten sonra tanımlanan adresin gelmesi gerekiyor ama gelmiyorsa aşağıdaki komutu yazarak sahibi olduğumuz cloud hesabına atanan bütün adresleri görebiliriz.

![9](https://user-images.githubusercontent.com/95285650/189103431-cfaeedb2-e2c3-4dfc-87e0-a656d4730893.png)

Google Cloud hesabımızdan da yaptığımız işlemleri takip edebiliriz:

Ingress:

![10](https://user-images.githubusercontent.com/95285650/189103592-ae46e4d0-f4b5-4155-bc3b-d78b9ddd745f.png)

Wordpress Volume Claim:

![11](https://user-images.githubusercontent.com/95285650/189103789-a0e01f9a-5185-42b8-905a-be11cd8946af.png)

Wordpress Volume Claim yaml dosyası:

![12](https://user-images.githubusercontent.com/95285650/189103978-fe32259d-6b83-4f33-ad57-5525f4d04bf8.png)










  
  
  
  
  
  
  
  
  
  
  
 
 
 


