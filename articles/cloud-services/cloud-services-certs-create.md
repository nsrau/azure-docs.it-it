<properties 
	pageTitle="Servizi cloud e certificati di gestione | Microsoft Azure" 
	description="Informazioni su come creare e usare i certificati con Microsoft Azure" 
	services="cloud-services" 
	documentationCenter=".net" 
	authors="Thraka" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/05/2016"
	ms.author="adegeo"/>

# Panoramica sui certificati per i servizi cloud di Azure
I [certificati di servizio](#what-are-service-certificates) vengono usati in Azure per i servizi cloud, mentre i [certificati di gestione](#what-are-management-certificates) vengono usati per l'autenticazione con l'API di gestione e sono legati all'uso del portale di Azure classico e non di Gestione risorse di Azure. Questo argomento fornisce informazioni generali su entrambi i tipi di certificati, su come [crearli](#create) e come [distribuirli](#deploy) in Azure.

Quelli usati in Azure sono certificati x.509 v3 e possono essere firmati da un altro certificato attendibile o essere autofirmati. Un certificato autofirmato viene firmato dal creatore e pertanto non è attendibile per impostazione predefinita. La maggior parte dei browser può ignorarlo. I certificati autofirmati dovrebbero essere usati solo dall'utente quando sviluppa e testa i servizi cloud.

I certificati usati da Azure possono contenere una chiave privata o una pubblica. I certificati hanno un'identificazione personale che consente di identificarli in modo non ambiguo. Questa identificazione personale viene usata nel [file di configurazione](cloud-services-configure-ssl-certificate.md) di Azure per identificare il certificato che un servizio cloud dovrebbe usare.

## Cosa sono i certificati di servizio?
I certificati di servizio sono associati ai servizi cloud e consentono la comunicazione sicura verso e dal servizio. Ad esempio, se si distribuisse un ruolo Web, si fornirebbe un certificato per autenticare un endpoint HTTPS esposto. I certificati di servizio, definiti nella definizione del servizio, vengono automaticamente distribuiti nella macchina virtuale che esegue un'istanza del ruolo.

È possibile caricare i certificati di servizio nel portale di Azure classico usando il portale di Azure classico o usando l'API di gestione dei servizi. I certificati di servizio vengono associati a un servizio cloud specifico e assegnati a una distribuzione nel file di definizione del servizio.

I certificati di servizio possono essere gestiti separatamente dai servizi e da persone diverse. Ad esempio, uno sviluppatore può caricare un pacchetto del servizio che fa riferimento a un certificato caricato in precedenza in Azure da un responsabile IT. Un responsabile IT può gestire e rinnovare tale certificato modificando la configurazione del servizio senza dover caricare un nuovo pacchetto del servizio. Questa operazione è possibile poiché il nome logico per il certificato e il relativo nome di archivio e il percorso vengono specificati nel file di definizione del servizio, mentre l'identificazione personale del certificato viene specificata nel file di configurazione del servizio. Per aggiornare il certificato, è sufficiente caricare un nuovo certificato e modificare il valore dell'identificazione personale nel file di configurazione del servizio.

## Cosa sono i certificati di gestione?
I certificati di gestione consentono di eseguire l'autenticazione con l'API di gestione dei servizi fornita da Azure classico. Molti programmi e strumenti (ad esempio Visual Studio o Azure SDK) useranno questi certificati per automatizzare la configurazione e la distribuzione dei vari servizi di Azure. Questi non sono realmente correlati ai servizi cloud.

>[AZURE.WARNING] Fare attenzione. Questi tipi di certificati consentono a chiunque esegua l'autenticazione di gestire la sottoscrizione a cui sono associati.

### Limitazioni
È previsto un limite di 100 certificati di gestione per ogni sottoscrizione. È anche previsto un limite di 100 certificati di gestione per tutte le sottoscrizioni che fanno riferimento all'ID utente di un amministratore del servizio specifico. Se l'ID utente per l'amministratore dell'account è già stato usato per aggiungere 100 certificati di gestione e ne sono necessari altri, è possibile aggiungere un coamministratore per aumentare il numero di certificati.

Prima di aggiungere più di 100 certificati, verificare se è possibile riutilizzarne uno esistente. L'uso di coamministratori potrebbe rendere più complesso il processo di gestione dei certificati.


<a name="create"></a>
## Creare un nuovo certificato autofirmato
È possibile usare qualsiasi strumento disponibile per creare un certificato autofirmato purché rispetti queste impostazioni:

* Un certificato X.509.
* Contiene una chiave privata.
* Viene creato per lo scambio di chiave (file PFX).
* Il nome del soggetto deve corrispondere al dominio usato per accedere al servizio cloud.
    > Non è possibile ottenere un certificato SSL per il dominio cloudapp.net o per domini di Azure. Il nome del soggetto del certificato deve pertanto corrispondere al nome di dominio personalizzato usato per accedere all'applicazione. Ad esempio,**contoso.net**e non**contoso.cloudapp.net**.
* Crittografia minima a 2048 bit.
* **Solo certificato di servizio**: il certificato lato client deve trovarsi nell'archivio certificati *Personale*.

Sono disponibili due semplici modi per creare un certificato in Windows: con l'utilità `makecert.exe` o con IIS.

### Makecert.exe

Questa utilità è stata deprecata e di seguito non è più disponibile la relativa documentazione. Per altre informazioni, vedere [questo articolo di MSDN](https://msdn.microsoft.com/library/windows/desktop/aa386968).

### PowerShell

```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```

>[AZURE.NOTE] Se si vuole usare il certificato con un indirizzo IP anziché un dominio, usare l'indirizzo IP nel parametro -DnsName.


Se si desidera usare questo [certificato con il portale di gestione](../azure-api-management-certs.md), esportarlo in un file con estensione **cer**:

```powershell
Export-Certificate -Type CERT -Cert $cert -FilePath .\my-cert-file.cer
```

### Internet Information Services (IIS)

Su Internet sono disponibili molte pagine che spiegano come eseguire questa operazione con IIS. [Qui](https://www.sslshopper.com/article-how-to-create-a-self-signed-certificate-in-iis-7.html)ce n’è uno ottimo che ho trovato che ritengo che spieghi bene.

### Java
È possibile usare Java per [creare un certificato](../app-service-web/java-create-azure-website-using-java-sdk.md#create-a-certificate).

### Linux
[Questo](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md) articolo descrive come creare certificati tramite SSH.

## Passaggi successivi

[Caricare il certificato di servizio nel portale di Azure classico](cloud-services-configure-ssl-certificate.md) o nel [portale di Azure](cloud-services-configure-ssl-certificate-portal.md).

Caricare il [certificato dell'API di gestione](../azure-api-management-certs.md) nel portale di Azure classico.

>[AZURE.NOTE] Il portale di Azure non usa i certificati di gestione per accedere all'API, ma usa invece gli account utente.

<!---HONumber=AcomDC_0914_2016-->