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

Terraform ile GKE Oluşturmak
Desktop ta bootcamp adlı bir dosya oluşturduk. Vscode da bu dosyayı açtık ve sağ tıklayarak yeni bir file oluşturuyoruz. File ismi main.tf -.tf uzantılı olması terraform kodlu olacağı anlamına geliyor- . Bu dosyamızı şu şekilde yazıyoruz.
![9](https://user-images.githubusercontent.com/95285650/188979213-32f2ad7c-bce5-41e6-becd-8415b6dd2b6b.png)
![10](https://user-images.githubusercontent.com/95285650/188979359-146e2ec3-ea98-4159-bb3e-8e67e3d7860e.png)

Provider, projenin adı, bölge ve zone seçiyoruz. Bize en yakın olan bölge Frankfurt olduğundan europe-west3 seçtik. 
Compute instanse ismi bootcamp olarak belirledik, makine tipi de e2-micro seçtik-disk imajımızı ubuntu 20.04 kullandığımdan bunu seçtim-. 
Network interface oluşturduk.
Networkü google_compute_network satırında oluşturduk. Subnetleri otomatik oluşturdu. Böylelikle sanal makine oluşturdu içinde subnetler otomatik olacak ve içinde vm açacak.
Boot disk satırında vm (sanal makine) açıyor-ubuntu olarak belirledik-.
Bunları tamamladıktan sonra vscod terminalinde:

-terraform plan –out plan.out 

Kodunu yazıyoruz, kendi oluşturduğu planı getiriyor ve bu planı uygulamaya döküyor. 

-terraform apply "plan.out" 

Bu kodu yazdığımızda ise planı uygulatmış oluyoruz.



  
  
  
  
  
  
  
  
  
  
  
 
 
 


