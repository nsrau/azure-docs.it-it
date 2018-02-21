---
title: Associare un certificato SSL personalizzato esistente ad app Web di Azure | Microsoft Docs
description: Informazioni su come associare un certificato SSL personalizzato all'app Web, al back-end di un'app per dispositivi mobili o all'app per le API nel Servizio app di Azure.
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: 5d5bf588-b0bb-4c6d-8840-1b609cfb5750
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 11/30/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: f69bc731b2858c338d7f7b4d347e7107a0f4eeed
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2017
---
# <a name="bind-an-existing-custom-ssl-certificate-to-azure-web-apps"></a>Associare un certificato SSL personalizzato esistente ad app Web di Azure

Le app Web di Azure offrono un servizio di hosting Web ad alta scalabilità e con funzioni di auto-correzione. Questa esercitazione illustra come associare alle [app Web di Azure](app-service-web-overview.md) un certificato SSL personalizzato acquistato presso un'autorità di certificazione attendibile. Al termine, si sarà in grado di accedere all'app Web dall'endpoint HTTPS del dominio DNS personalizzato.

![App Web con certificato SSL personalizzato](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Aggiornare il piano tariffario dell'app
> * Associare il certificato SSL personalizzato al servizio app
> * Imporre HTTPS per l'app
> * Automatizzare l'associazione dei certificati SSL con gli script

> [!NOTE]
> Se è necessario usare un certificato SSL personalizzato, è possibile ottenerlo direttamente nel portale di Azure e associarlo all'app Web. Seguire l'[esercitazione sui certificati del Servizio app](web-sites-purchase-ssl-web-site.md).

## <a name="prerequisites"></a>prerequisiti

Per completare questa esercitazione:

- [Creare un'app del Servizio app](/azure/app-service/)
- [Eseguire il mapping di un nome DNS personalizzato all'app Web](app-service-web-tutorial-custom-domain.md)
- Acquisire un certificato SSL da un'autorità di certificazione attendibile
- Disporre della chiave privata usata per firmare la richiesta di certificato SSL

<a name="requirements"></a>

### <a name="requirements-for-your-ssl-certificate"></a>Requisiti per il certificato SSL

Per poter essere usato nel servizio app, il certificato deve soddisfare tutti i requisiti seguenti:

* Deve essere firmato da un'autorità di certificazione attendibile
* Deve essere esportato come file PFX protetto da password
* Deve contenere una chiave privata costituita da almeno 2048 bit
* Deve contenere tutti i certificati intermedi nella catena di certificati.

> [!NOTE]
> Sebbene non siano descritti in questo articolo, i **certificati di crittografia a curva ellittica (ECC)** possono essere usati con il servizio app. Per informazioni sulla procedura per creare i certificati ECC, rivolgersi all'autorità di certificazione.

## <a name="prepare-your-web-app"></a>Preparare l'app Web

Per associare un certificato SSL personalizzato all'app Web, il [piano di servizio app](https://azure.microsoft.com/pricing/details/app-service/) in uso deve essere di livello **Basic**, **Standard** o **Premium**. In questo passaggio si verifica che l'app Web sia supportata dal piano tariffario adeguato.

### <a name="log-in-to-azure"></a>Accedere ad Azure

Aprire il [portale di Azure](https://portal.azure.com).

### <a name="navigate-to-your-web-app"></a>Accedere all'app Web

Nel menu a sinistra fare clic su **Servizi app** e quindi sul nome dell'app Web.

![Selezionare l'app Web](./media/app-service-web-tutorial-custom-ssl/select-app.png)

Viene visualizzata la pagina di gestione dell'app Web.  

### <a name="check-the-pricing-tier"></a>Scegliere il piano tariffario

Nel riquadro di spostamento a sinistra della pagina dell'app Web scorrere fino alla sezione **Impostazioni** e selezionare **Scala verticalmente (piano di servizio app)**.

![Menu di scalabilità verticale](./media/app-service-web-tutorial-custom-ssl/scale-up-menu.png)

Verificare che l'app Web non sia inclusa nel livello **Gratuito** o **Condiviso**. Il livello corrente dell'app Web è evidenziato da una casella blu scuro.

![Controllare il piano tariffario](./media/app-service-web-tutorial-custom-ssl/check-pricing-tier.png)

Il certificato SSL personalizzato non è supportato nel livello **Gratuito** o **Condiviso**. Se è necessario passare a un livello superiore, seguire la procedura della sezione successiva. In caso contrario, chiudere la pagina **Scegliere il livello di prezzo** e passare a [Caricare il certificato SSL e Associare il certificato SSL](#upload).

### <a name="scale-up-your-app-service-plan"></a>Passare a un piano di servizio app superiore

Selezionare uno tra i livelli **Basic**, **Standard** o **Premium**.

Fare clic su **Seleziona**.

![Scegliere un piano tariffario](./media/app-service-web-tutorial-custom-ssl/choose-pricing-tier.png)

La visualizzazione della notifica seguente indica che l'operazione di passaggio al livello superiore è stata completata.

![Notifica di passaggio al livello superiore](./media/app-service-web-tutorial-custom-ssl/scale-notification.png)

<a name="upload"></a>

## <a name="bind-your-ssl-certificate"></a>Associare il certificato SSL

Si è ora pronti a caricare il certificato SSL nell'app Web.

### <a name="merge-intermediate-certificates"></a>Unire i certificati intermedi

Se l'autorità di certificazione offre più certificati nella catena di certificati, è necessario unire i certificati nell'ordine. 

A tale scopo, aprire ogni certificato ricevuto in un editor di testo. 

Creare un file per il certificato unito denominato _mergedcertificate.crt_. In un editor di testo copiare il contenuto di ogni certificato nel file. L'ordine dei certificati deve corrispondere all'ordine nella catena di certificati, che inizia con il certificato dell'utente e termina con il certificato radice. Sarà simile a quanto indicato nell'esempio seguente:

```
-----BEGIN CERTIFICATE-----
<your entire Base64 encoded SSL certificate>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 1>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 2>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded root certificate>
-----END CERTIFICATE-----
```

### <a name="export-certificate-to-pfx"></a>Esportare il certificato in un file PFX

Esportare il certificato SSL unito con la chiave privata con cui è stata generata la richiesta del certificato.

Se è stato usato OpenSSL per generare la richiesta del certificato, è stato creato un file di chiave privata. Per esportare il certificato in un file PFX, eseguire il comando seguente. Sostituire i segnaposto _&lt;private-key-file>_ e _&lt;merged-certificate-file>_ con i percorsi della chiave privata e del file di certificato unito.

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>  
```

Quando richiesto, definire una password di esportazione. La password verrà usata in seguito durante il caricamento del certificato SSL nel servizio app.

se è stato usato IIS o _Certreq.exe_ per generare la richiesta di certificato, installare il certificato nel computer locale e quindi [esportarlo in un file PFX](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx).

### <a name="upload-your-ssl-certificate"></a>Caricare il certificato SSL

Per caricare il certificato SSL, fare clic su **Certificati SSL** nel riquadro di spostamento sinistro dell'app Web.

Fare clic su **Carica certificato**. 

In **File del certificato PFX** selezionare il file PFX. In **Password certificato** digitare la password creata durante l'esportazione del file PFX.

Fare clic su **Carica**.

![Caricamento del certificato](./media/app-service-web-tutorial-custom-ssl/upload-certificate-private1.png)

Dopo che il Servizio app ha terminato il caricamento del certificato, viene visualizzata la pagina **Certificati SSL**.

![Certificato caricato](./media/app-service-web-tutorial-custom-ssl/certificate-uploaded.png)

### <a name="bind-your-ssl-certificate"></a>Associare il certificato SSL

Nella sezione **Associazioni SSL** fare clic su **Aggiungi l'associazione**.

Nel pannello **Aggiungi associazione SSL** usare gli elenchi a discesa per selezionare il nome di dominio da proteggere e il certificato da usare.

> [!NOTE]
> Se il certificato è stato caricato ma i nomi di dominio non appaiono nell'elenco a discesa **Nome host**, provare ad aggiornare la pagina del browser.
>
>

In **Tipo SSL** selezionare se usare l'SSL basato su **[indicazione nome server (SNI)](http://en.wikipedia.org/wiki/Server_Name_Indication)** o basato su IP.

- **SSL basato su SNI**: è possibile aggiungere più associazioni SSL basate su SNI. Questa opzione consente di usare più certificati SSL per proteggere più domini nello stesso indirizzo IP. La maggior parte dei browser moderni (tra cui Internet Explorer, Chrome, Firefox e Opera) supporta SNI. Per altre informazioni sul supporto dei browser, vedere [Indicazione nome server](http://wikipedia.org/wiki/Server_Name_Indication).
- **SSL basato su IP**: è possibile aggiungere una sola associazione SSL basata su IP. Questa opzione consente di usare solo un certificato SSL per proteggere un indirizzo IP pubblico dedicato. Per proteggere più domini, è necessario proteggerli tutti usando lo stesso certificato SSL. Questa è l'opzione tradizionale per l'associazione SSL.

Fare clic su **Aggiungi l'associazione**.

![Associare un certificato SSL](./media/app-service-web-tutorial-custom-ssl/bind-certificate.png)

Al termine del caricamento da parte del Servizio app, il certificato viene visualizzato nella sezione **Certificati SSL**.

![Certificato associato all'app Web](./media/app-service-web-tutorial-custom-ssl/certificate-bound.png)

## <a name="remap-a-record-for-ip-ssl"></a>Eseguire nuovamente il mapping di un record A per IP SSL

Se non si usa l'SSL basato su IP nell'app Web, passare direttamente alla sezione [Testare HTTPS per il dominio personalizzato](#test).

Per impostazione predefinita, l'app Web usa un indirizzo IP pubblico condiviso. Quando si associa un certificato con SSL basato su IP, il servizio app crea un nuovo indirizzo IP dedicato per l'app Web.

Se è stato eseguito il mapping di un record A all'app Web, aggiornare il Registro di sistema del dominio con questo nuovo indirizzo IP dedicato.

La pagina **Dominio personalizzato** dell'app Web viene aggiornata con il nuovo indirizzo IP dedicato. [Copiare questo indirizzo IP](app-service-web-tutorial-custom-domain.md#info), quindi [eseguire nuovamente il mapping del record A](app-service-web-tutorial-custom-domain.md#map-an-a-record) a questo indirizzo IP.

<a name="test"></a>

## <a name="test-https"></a>Testare HTTPS

A questo punto, non resta che assicurarsi che HTTPS funzioni correttamente per il dominio personalizzato. In diversi browser passare a `https://<your.custom.domain>` per verificare che l'app Web sia gestita.

![Passaggio all'app di Azure nel portale](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

> [!NOTE]
> Se l'app Web visualizza errori di convalida del certificato, è probabile che si stia usando un certificato autofirmato.
>
> Se non è questo il motivo, è possibile che siano stati esclusi i certificati intermedi durante l'esportazione del certificato nel file PFX.

<a name="bkmk_enforce"></a>

## <a name="enforce-https"></a>Applicare HTTPS

Per impostazione predefinita, gli utenti possono continuare ad accedere all'app Web usando il protocollo HTTP. È possibile reindirizzare tutte le richieste HTTP alla porta HTTPS.

Nel riquadro di spostamento a sinistra della pagina dell'app Web selezionare **Domini personalizzati**. Quindi in **Solo HTTPS**, selezionare **Attiva**.

![Applicare HTTPS](./media/app-service-web-tutorial-custom-ssl/enforce-https.png)

Al termine dell'operazione, scegliere uno degli URL HTTP che fanno riferimento all'applicazione. Ad esempio: 

- `http://<app_name>.azurewebsites.net`
- `http://contoso.com`
- `http://www.contoso.com`

## <a name="automate-with-scripts"></a>Automatizzazione con gli script

È possibile automatizzare le associazioni SSL per l'app Web con gli script usando l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) o [Azure PowerShell](/powershell/azure/overview).

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Il comando seguente carica un file PFX esportato e ottiene l'identificazione personale.

```bash
thumbprint=$(az webapp config ssl upload \
    --name <app_name> \
    --resource-group <resource_group_name> \
    --certificate-file <path_to_PFX_file> \
    --certificate-password <PFX_password> \
    --query thumbprint \
    --output tsv)
```

Il comando seguente aggiunge un'associazione SSL basata su SNI, usando l'identificazione personale ottenuta con il comando precedente.

```bash
az webapp config ssl bind \
    --name <app_name> \
    --resource-group <resource_group_name>
    --certificate-thumbprint $thumbprint \
    --ssl-type SNI \
```

### <a name="azure-powershell"></a>Azure PowerShell

Il comando seguente carica un file PFX esportato e aggiunge un'associazione SSL basata su SNI.

```PowerShell
New-AzureRmWebAppSSLBinding `
    -WebAppName <app_name> `
    -ResourceGroupName <resource_group_name> `
    -Name <dns_name> `
    -CertificateFilePath <path_to_PFX_file> `
    -CertificatePassword <PFX_password> `
    -SslState SniEnabled
```
## <a name="public-certificates-optional"></a>Certificati pubblici (facoltativo)
È possibile caricare i [certificati pubblici](https://blogs.msdn.microsoft.com/appserviceteam/2017/11/01/app-service-certificates-now-supports-public-certificates-cer/) nell'app Web. È possibile usare i certificati pubblici per le app anche nell'Ambiente del servizio app. Se si intende archiviare il certificato nell'archivio certificati LocalMachine, è necessario usare un'app Web nell'Ambiente del servizio app. Per altre informazioni, vedere [Come configurare i certificati pubblici nell'App Web](https://blogs.msdn.microsoft.com/appserviceteam/2017/11/01/app-service-certificates-now-supports-public-certificates-cer).

![Caricare un certificato pubblico](./media/app-service-web-tutorial-custom-ssl/upload-certificate-public1.png)

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Aggiornare il piano tariffario dell'app
> * Associare il certificato SSL personalizzato al servizio app
> * Imporre HTTPS per l'app
> * Automatizzare l'associazione dei certificati SSL con gli script

Passare all'esercitazione successiva per imparare a usare la rete di distribuzione dei contenuti di Azure.

> [!div class="nextstepaction"]
> [Aggiungere una rete per la distribuzione di contenuti (CDN) a un servizio app di Azure](app-service-web-tutorial-content-delivery-network.md)

Per altre informazioni, vedere l'articolo relativo all'[utilizzo di un certificato SSL nel codice dell'applicazione nel servizio app di Azure](app-service-web-ssl-cert-load.md).