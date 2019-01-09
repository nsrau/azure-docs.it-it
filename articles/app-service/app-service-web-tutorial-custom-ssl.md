---
title: Associare un certificato SSL personalizzato esistente - Servizio app di Azure | Microsoft Docs
description: Informazioni su come associare un certificato SSL personalizzato all'app Web, al back-end di un'app per dispositivi mobili o all'app per le API nel Servizio app di Azure.
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: ''
ms.assetid: 5d5bf588-b0bb-4c6d-8840-1b609cfb5750
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/24/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: cdd73c46d87ec09439188024945bd60299bb1d57
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/19/2018
ms.locfileid: "53629748"
---
# <a name="tutorial-bind-an-existing-custom-ssl-certificate-to-azure-app-service"></a>Esercitazione: Associare un certificato SSL personalizzato esistente a Servizio app di Azure

Servizio app di Azure offre un servizio di hosting Web con scalabilità elevata e funzioni di auto-correzione. Questa esercitazione illustra come associare un certificato SSL personalizzato acquistato presso un'autorità di certificazione attendibile a [Servizio app di Azure](overview.md). Al termine, si sarà in grado di accedere all'app dall'endpoint HTTPS del dominio DNS personalizzato.

![App Web con certificato SSL personalizzato](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Aggiornare il piano tariffario dell'app
> * Associare il certificato personalizzato al servizio app
> * Rinnovare i certificati
> * Applicare HTTPS
> * Applicare TLS 1.1/1.2
> * Automatizzare la gestione TLS con script

> [!NOTE]
> Se è necessario un certificato SSL personalizzato, è possibile ottenerlo direttamente nel portale di Azure e associarlo all'app. Seguire l'[esercitazione sui certificati del Servizio app](web-sites-purchase-ssl-web-site.md).

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione:

- [Creare un'app del Servizio app](/azure/app-service/)
- [Eseguire il mapping di un nome DNS personalizzato all'app del servizio app](app-service-web-tutorial-custom-domain.md)
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

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

<a name="upload"></a>

## <a name="bind-your-ssl-certificate"></a>Associare il certificato SSL

Si è ora pronti a caricare il certificato SSL nell'app.

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

Per caricare il certificato SSL, fare clic su **Impostazioni SSL** nel riquadro di spostamento sinistro dell'app.

Fare clic su **Carica certificato**. 

In **File del certificato PFX** selezionare il file PFX. In **Password certificato** digitare la password creata durante l'esportazione del file PFX.

Fare clic su **Carica**.

![Caricamento del certificato](./media/app-service-web-tutorial-custom-ssl/upload-certificate-private1.png)

Dopo che il Servizio app ha terminato il caricamento del certificato, viene visualizzata la pagina **Impostazioni SSL**.

![Certificato caricato](./media/app-service-web-tutorial-custom-ssl/certificate-uploaded.png)

### <a name="bind-your-ssl-certificate"></a>Associare il certificato SSL

Nella sezione **Associazioni SSL** fare clic su **Aggiungi l'associazione**.

Nel pannello **Aggiungi associazione SSL** usare gli elenchi a discesa per selezionare il nome di dominio da proteggere e il certificato da usare.

> [!NOTE]
> Se il certificato è stato caricato ma i nomi di dominio non appaiono nell'elenco a discesa **Nome host**, provare ad aggiornare la pagina del browser.
>
>

In **Tipo SSL** selezionare se usare l'SSL basato su **[indicazione nome server (SNI)](https://en.wikipedia.org/wiki/Server_Name_Indication)** o basato su IP.

- **SSL basato su SNI**: è possibile aggiungere più associazioni SSL basate su SNI. Questa opzione consente di usare più certificati SSL per proteggere più domini nello stesso indirizzo IP. La maggior parte dei browser moderni (tra cui Internet Explorer, Chrome, Firefox e Opera) supporta SNI. Per altre informazioni sul supporto dei browser, vedere [Indicazione nome server](https://wikipedia.org/wiki/Server_Name_Indication).
- **SSL basato su IP**: è possibile aggiungere una sola associazione SSL basata su IP. Questa opzione consente di usare solo un certificato SSL per proteggere un indirizzo IP pubblico dedicato. Per proteggere più domini, è necessario proteggerli tutti usando lo stesso certificato SSL. Questa è l'opzione tradizionale per l'associazione SSL.

Fare clic su **Aggiungi l'associazione**.

![Associare un certificato SSL](./media/app-service-web-tutorial-custom-ssl/bind-certificate.png)

Al termine del caricamento da parte del Servizio app, il certificato viene visualizzato nella sezione **Certificati SSL**.

![Certificato associato all'app Web](./media/app-service-web-tutorial-custom-ssl/certificate-bound.png)

## <a name="remap-a-record-for-ip-ssl"></a>Eseguire nuovamente il mapping di un record A per IP SSL

Se non si usa il protocollo SSL basato su IP nell'app, passare alla sezione [Testare HTTPS per il dominio personalizzato](#test).

Per impostazione predefinita, l'app usa un indirizzo IP pubblico condiviso. Quando si associa un certificato con il protocollo SSL basato su IP, il servizio app crea un nuovo indirizzo IP dedicato per l'app.

Se è stato eseguito il mapping di un record A all'app, aggiornare il Registro di sistema del dominio con questo nuovo indirizzo IP dedicato.

La pagina **Dominio personalizzato** dell'app viene aggiornata con il nuovo indirizzo IP dedicato. [Copiare questo indirizzo IP](app-service-web-tutorial-custom-domain.md#info), quindi [eseguire nuovamente il mapping del record A](app-service-web-tutorial-custom-domain.md#map-an-a-record) a questo indirizzo IP.

<a name="test"></a>

## <a name="test-https"></a>Testare HTTPS

A questo punto, non resta che assicurarsi che HTTPS funzioni correttamente per il dominio personalizzato. In diversi browser passare a `https://<your.custom.domain>` per verificare che l'app sia gestita.

![Passaggio all'app di Azure nel portale](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

> [!NOTE]
> Se l'app restituisce errori di convalida del certificato, è probabile che sia in uso un certificato autofirmato.
>
> Se non è questo il motivo, è possibile che siano stati esclusi i certificati intermedi durante l'esportazione del certificato nel file PFX.

<a name="bkmk_enforce"></a>

## <a name="renew-certificates"></a>Rinnovare i certificati

L'indirizzo IP in ingresso può essere modificato quando si elimina un'associazione, anche se tale associazione è basata su IP. Questo aspetto è particolarmente importante quando si rinnova un certificato che si trova già in un'associazione basata su IP. Per evitare una modifica dell'indirizzo IP dell'app, seguire questi passaggi nell'ordine indicato:

1. Caricare il nuovo certificato.
2. Associare il nuovo certificato al dominio personalizzato desiderato senza eliminare quello precedente. Questa operazione sostituisce l'associazione anziché rimuovere quella precedente.
3. Eliminare il certificato precedente. 

## <a name="enforce-https"></a>Applicare HTTPS

Per impostazione predefinita, gli utenti possono continuare ad accedere all'app usando il protocollo HTTP. È possibile reindirizzare tutte le richieste HTTP alla porta HTTPS.

Nel riquadro di spostamento a sinistra della pagina dell'app selezionare **Impostazioni SSL**. Quindi in **Solo HTTPS**, selezionare **Attiva**.

![Applicare HTTPS](./media/app-service-web-tutorial-custom-ssl/enforce-https.png)

Al termine dell'operazione, scegliere uno degli URL HTTP che fanno riferimento all'applicazione. Ad esempio: 

- `http://<app_name>.azurewebsites.net`
- `http://contoso.com`
- `http://www.contoso.com`

## <a name="enforce-tls-versions"></a>Applicare versioni di TLS

L'app consente [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.2 per impostazione predefinita, che è il livello TLS consigliato dagli standard di settore, ad esempio [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). Per applicare versioni di TLS diverse, seguire questa procedura:

Nel riquadro di spostamento a sinistra della pagina dell'app selezionare **Impostazioni SSL**. In **TLS version** (Versione TLS) selezionare la versione minima di TLS da usare. Questa impostazione controlla solo le chiamate in ingresso. 

![Applicare TLS 1.1 o 1.2](./media/app-service-web-tutorial-custom-ssl/enforce-tls1.2.png)

Al termine dell'operazione, l'app rifiuta tutte le connessioni con versioni di TLS meno recenti.

## <a name="automate-with-scripts"></a>Automatizzazione con gli script

È possibile automatizzare le associazioni SSL per l'app con gli script, usando l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) o [Azure PowerShell](/powershell/azure/overview).

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

Il comando seguente consente di applicare almeno la versione 1.2 di TLS

```bash
az webapp config set \
    --name <app_name> \
    --resource-group <resource_group_name>
    --min-tls-version 1.2
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
Se l'app deve accedere alle risorse remote come un client e la risorsa remota richiede l'autenticazione del certificato, è possibile caricare i [certificati pubblici](https://blogs.msdn.microsoft.com/appserviceteam/2017/11/01/app-service-certificates-now-supports-public-certificates-cer/) nell'app. I certificati pubblici non sono necessari per le associazioni SSL dell'app.

Per maggiori dettagli sul caricamento e sull'uso di un certificato pubblico nell'app, vedere [Usare un certificato SSL nel codice dell'applicazione in Servizio app di Azure](app-service-web-ssl-cert-load.md). È possibile usare i certificati pubblici con le app anche negli ambienti del servizio app. Se è necessario archiviare il certificato nell'archivio certificati LocalMachine, usare un'app nell'ambiente del servizio app. Per altre informazioni, vedere [How to configure public certificates to your App Service app](https://blogs.msdn.microsoft.com/appserviceteam/2017/11/01/app-service-certificates-now-supports-public-certificates-cer) (Come configurare i certificati pubblici nell'app del servizio app).

![Caricare un certificato pubblico](./media/app-service-web-tutorial-custom-ssl/upload-certificate-public1.png)

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Aggiornare il piano tariffario dell'app
> * Associare il certificato personalizzato al servizio app
> * Rinnovare i certificati
> * Applicare HTTPS
> * Applicare TLS 1.1/1.2
> * Automatizzare la gestione TLS con script

Passare all'esercitazione successiva per imparare a usare la rete di distribuzione dei contenuti di Azure.

> [!div class="nextstepaction"]
> [Aggiungere una rete per la distribuzione di contenuti (CDN) a un servizio app di Azure](../cdn/cdn-add-to-web-app.md)

Per altre informazioni, vedere l'articolo relativo all'[utilizzo di un certificato SSL nel codice dell'applicazione nel servizio app di Azure](app-service-web-ssl-cert-load.md).
