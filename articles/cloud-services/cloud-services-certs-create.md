---
title: Servizi cloud e certificati di gestione | Documentazione Microsoft
description: Informazioni su come creare e usare i certificati con Microsoft Azure
services: cloud-services
documentationcenter: .net
author: Thraka
manager: timlt
editor: 
ms.assetid: fc70d00d-899b-4771-855f-44574dc4bfc6
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: adegeo
ms.openlocfilehash: 689977491e6df37e48536f59234bf4ddba6a1575
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="certificates-overview-for-azure-cloud-services"></a>Panoramica sui certificati per i servizi cloud di Azure
I certificati vengono utilizzati in Azure per i servizi cloud ([certificati di servizio](#what-are-service-certificates)) e per l'autenticazione con l'API di gestione ([certificati di gestione](#what-are-management-certificates) quando si usa il portale di Azure classico e non il portale di Azure non classico). Questo argomento offre informazioni generali su entrambi i tipi di certificati, su come [crearli](#create) e come [distribuirli](#deploy) in Azure.

Quelli usati in Azure sono certificati x.509 v3 e possono essere firmati da un altro certificato attendibile o essere autofirmati. Un certificato autofirmato viene firmato dall'autore e pertanto non è attendibile per impostazione predefinita. La maggior parte dei browser può ignorare questo problema. È consigliabile utilizzare i certificati autofirmati solo quando si sviluppano e si testano servizi cloud. 

I certificati usati da Azure possono contenere una chiave privata o una pubblica. I certificati hanno un'identificazione personale che consente di identificarli in modo non ambiguo. Questa identificazione personale viene usata nel [file di configurazione](cloud-services-configure-ssl-certificate.md) di Azure per identificare il certificato che un servizio cloud dovrebbe usare. 

## <a name="what-are-service-certificates"></a>Cosa sono i certificati di servizio?
I certificati di servizio sono associati ai servizi cloud e consentono la comunicazione sicura verso e dal servizio. Ad esempio, se si distribuisse un ruolo Web, si fornirebbe un certificato per autenticare un endpoint HTTPS esposto. I certificati di servizio, definiti nella definizione del servizio, vengono automaticamente distribuiti nella macchina virtuale che esegue un'istanza del ruolo. 

È possibile caricare i certificati di servizio nel portale di Azure classico tramite il portale di Azure classico o usando il modello di distribuzione classico. I certificati di servizio sono associati a uno specifico servizio cloud. Vengono assegnati a una distribuzione nel file di definizione del servizio.

I certificati di servizio possono essere gestiti separatamente dai servizi e da persone diverse. Ad esempio, uno sviluppatore può caricare un pacchetto del servizio che fa riferimento a un certificato caricato in precedenza in Azure da un responsabile IT. Un responsabile IT può gestire e rinnovare tale certificato, modificando la configurazione del servizio, senza dover caricare un nuovo pacchetto del servizio. Questa operazione è possibile poiché il nome logico, il nome di archivio e il percorso vengono specificati nel file di definizione del servizio, mentre l'identificazione personale del certificato viene specificata nel file di configurazione del servizio. Per aggiornare il certificato, è sufficiente caricare un nuovo certificato e modificare il valore dell'identificazione personale nel file di configurazione del servizio.

>[!Note]
>L'articolo [domande frequenti su Servizi cloud](cloud-services-faq.md) contiene alcune utili informazioni sui certificati.

## <a name="what-are-management-certificates"></a>Cosa sono i certificati di gestione?
I certificati di gestione consentono di eseguire l'autenticazione con il modello di distribuzione classico. Molti programmi e strumenti (ad esempio Visual Studio o Azure SDK) usano questi certificati per automatizzare la configurazione e la distribuzione di vari servizi di Azure. Questi non sono realmente correlati ai servizi cloud. 

> [!WARNING]
> Fare attenzione. Questi tipi di certificati consentono a chiunque esegua l'autenticazione di gestire la sottoscrizione a cui sono associati. 
> 
> 

### <a name="limitations"></a>Limitazioni
È previsto un limite di 100 certificati di gestione per ogni sottoscrizione. È anche previsto un limite di 100 certificati di gestione per tutte le sottoscrizioni che fanno riferimento all'ID utente di un amministratore del servizio specifico. Se l'ID utente per l'amministratore dell'account è già stato usato per aggiungere 100 certificati di gestione e ne sono necessari altri, è possibile aggiungere un coamministratore per aumentare il numero di certificati. 

Prima di aggiungere più di 100 certificati, verificare se è possibile riutilizzarne uno esistente. L'uso di coamministratori potrebbe rendere più complesso il processo di gestione dei certificati.

<a name="create"></a>
## <a name="create-a-new-self-signed-certificate"></a>Creare un nuovo certificato autofirmato
È possibile usare qualsiasi strumento disponibile per creare un certificato autofirmato purché rispetti queste impostazioni:

* Un certificato X.509.
* Contiene una chiave privata.
* Viene creato per lo scambio di chiave (file PFX).
* Il nome del soggetto deve corrispondere al dominio usato per accedere al servizio cloud.

    > Non è possibile ottenere un certificato SSL per il dominio cloudapp.net o per domini di Azure. Il nome del soggetto del certificato deve pertanto corrispondere al nome di dominio personalizzato usato per accedere all'applicazione. Ad esempio **contoso.net**, non **contoso.cloudapp.net**.

* Crittografia minima a 2048 bit.
* **Solo certificato di servizio**: il certificato lato client deve trovarsi nell'archivio certificati *Personale* .

Sono disponibili due semplici modi per creare un certificato in Windows: con l'utilità `makecert.exe` o con IIS.

### <a name="makecertexe"></a>Makecert.exe
Questa utilità è stata deprecata e di seguito non è più disponibile la relativa documentazione. Per altre informazioni, vedere [questo articolo di MSDN](https://msdn.microsoft.com/library/windows/desktop/aa386968).

### <a name="powershell"></a>PowerShell
```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```

> [!NOTE]
> Se si vuole usare il certificato con un indirizzo IP anziché un dominio, usare l'indirizzo IP nel parametro -DnsName.


Se si desidera usare questo [certificato con il portale di gestione](../azure-api-management-certs.md), esportarlo in un file con estensione **cer** :

```powershell
Export-Certificate -Type CERT -Cert $cert -FilePath .\my-cert-file.cer
```

### <a name="internet-information-services-iis"></a>Internet Information Services (IIS)
Su Internet sono disponibili molte pagine che spiegano come eseguire questa operazione con IIS. [Qui](https://www.sslshopper.com/article-how-to-create-a-self-signed-certificate-in-iis-7.html) ce n’è uno ottimo che ho trovato che ritengo che spieghi bene. 

### <a name="linux"></a>Linux
[Questo](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) articolo descrive come creare certificati tramite SSH.

## <a name="next-steps"></a>Passaggi successivi
[Caricare il certificato di servizio nel portale di Azure classico](cloud-services-configure-ssl-certificate.md) o nel [portale di Azure](cloud-services-configure-ssl-certificate-portal.md).

Caricare il [certificato dell'API di gestione](../azure-api-management-certs.md) nel portale di Azure classico. Il portale di Azure non usa i certificati di gestione per l'autenticazione.

