<properties 
	pageTitle="Servizi cloud di Azure - Tutto ciò che è necessario sapere sui certificati" 
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
	ms.date="07/16/2015"
	ms.author="adegeo"/>

# Panoramica sui certificati per i servizi cloud di Azure
I certificati vengono usati in Azure per i servizi cloud ([certificati di servizio](#what-are-service-certificates)) e per l'autenticazione con l'API di gestione ([certificati di gestione](#what-are-management-certificates)). Questo argomento fornisce informazioni generali sui tipi di certificato, su come [crearli](#create) e come [distribuirli](#deploy) in Azure.

Quelli usati in Azure sono certificati x.509 v3 e possono essere firmati da un altro certificato attendibile o essere autofirmati. Un certificato autofirmato viene firmato dal creatore e pertanto non è attendibile per impostazione predefinita. La maggior parte dei browser può ignorarlo. I certificati autofirmati dovrebbero essere usati solo dall'utente quando sviluppa e testa i servizi cloud.

I certificati usati da Azure possono contenere una chiave privata o una pubblica. I certificati hanno un'identificazione personale che consente di identificarli in modo non ambiguo. Questa identificazione personale viene usata nel [file di configurazione](cloud-services-configure-ssl-certificate.md) di Azure per identificare il certificato che un servizio cloud dovrebbe usare.

## Cosa sono i certificati di servizio?
I certificati di servizio sono associati ai servizi cloud e consentono la comunicazione sicura verso e dal servizio. Ad esempio, se si distribuisse un ruolo Web, si fornirebbe un certificato per autenticare un endpoint HTTPS esposto. I certificati di servizio, definiti nella definizione del servizio, vengono automaticamente distribuiti nella macchina virtuale che esegue un'istanza del ruolo.

È possibile caricare i certificati di servizio nel portale di gestione usando il portale di gestione o usando l'API di gestione dei servizi. I certificati di servizio vengono associati a un servizio cloud specifico e assegnati a una distribuzione nel file di definizione del servizio.

I certificati di servizio possono essere gestiti separatamente dai servizi e da persone diverse. Ad esempio, uno sviluppatore può caricare un pacchetto del servizio che fa riferimento a un certificato caricato in precedenza in Azure da un responsabile IT. Un responsabile IT può gestire e rinnovare tale certificato modificando la configurazione del servizio senza dover caricare un nuovo pacchetto del servizio. Questa operazione è possibile poiché il nome logico per il certificato e il relativo nome di archivio e il percorso vengono specificati nel file di definizione del servizio, mentre l'identificazione personale del certificato viene specificata nel file di configurazione del servizio. Per aggiornare il certificato, è sufficiente caricare un nuovo certificato e modificare il valore dell'identificazione personale nel file di configurazione del servizio.

## Cosa sono i certificati di gestione?
I certificati di gestione consentono di eseguire l'autenticazione con l'API di gestione dei servizi fornita da Azure. Molti programmi e strumenti (ad esempio Visual Studio o Azure SDK) useranno questi certificati per automatizzare la configurazione e la distribuzione dei vari servizi di Azure. Questi non sono realmente correlati ai servizi cloud.

>[AZURE.WARNING]Pertanto, prestare attenzione prima di effettuare questa operazione. Questi tipi di certificati consentono a chiunque si autentichi di gestire la sottoscrizione a cui sono associati.

### Limitazioni
Esiste un limite di 100 certificati di gestione per ogni sottoscrizione. Esiste anche un limite di 100 certificati di gestione per tutte le sottoscrizioni che fanno riferimento all'ID utente di un amministratore del servizio specifico. Se l'ID utente per l'amministratore dell'account è già stato usato per aggiungere 100 certificati di gestione e ne sono necessari altri, è possibile aggiungere un coamministratore per aumentare il numero di certificati.

Prima di aggiungere più di 100 certificati, verificare se è possibile riutilizzarne uno esistente. L'uso di coamministratori potrebbe rendere più complesso il processo di gestione dei certificati.


<a name="create"></a>
## Creare un nuovo certificato autofirmato
È possibile usare qualsiasi strumento disponibile per creare un certificato autofirmato purché rispetti queste impostazioni:

* Un certificato X.509.
* Contiene una chiave privata.
* Viene creato per lo scambio di chiave (file PFX).
* Il nome del soggetto deve corrispondere al dominio usato per accedere al servizio cloud.
    > **contoso.net****contoso.cloudapp.net**
* Crittografia minima a 2048 bit.
* **Solo certificato di servizio**: il certificato lato client deve trovarsi nell'archivio certificati *personale*.

Sono disponibili due semplici modi per creare un certificato in Windows: con l'utilità `makecert.exe` o con IIS.

### Makecert.exe

Questa utilità viene installata con Visual Studio 2013/2015. È un'utilità della console che consente di creare e installare i certificati. Se si avvia il collegamento **Prompt dei comandi per gli sviluppatori per VS2015** creato quando si installa Visual Studio, verrà visualizzato un prompt dei comandi con questo strumento nel percorso.

    makecert -sky exchange -r -n "CN=[CertificateName]" -pe -a sha1 -len 2048 -ss My -sv [CertificateName].pvk [CertificateName].cer


### Internet Information Services (IIS)

Su Internet sono disponibili molte pagine che spiegano come eseguire questa operazione con IIS. [Eccone](https://www.sslshopper.com/article-how-to-create-a-self-signed-certificate-in-iis-7.html) una che illustra molto bene come procedere.

### Java
È possibile usare Java per [creare un certificato](../app-service-web/java-create-azure-website-using-java-sdk.md#create-a-certificate).

## Passaggi successivi

[Caricare il certificato di servizio nel portale di Azure](cloud-services-configure-ssl-certificate.md) (o nel [portale di anteprima](cloud-services-configure-ssl-certificate-portal.md)) e configurarlo per il servizio cloud.
 
Caricare il [certificato dell'API di gestione](../azure-api-management-certs.md) nel portale di Azure.

>[AZURE.NOTE]Il portale di anteprima di Azure non usa i certificati di gestione per accedere all'API, ma usa invece gli account utente.

<!---HONumber=July15_HO4-->