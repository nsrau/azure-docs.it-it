---
title: Caricare e associare certificati SSL - Servizio app di Azure | Microsoft Docs
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
ms.openlocfilehash: e0ee1e7c652ddf4126fc9658bf17d3e919d7a5c8
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475321"
---
# <a name="tutorial-upload-and-bind-ssl-certificates-to-azure-app-service"></a>Esercitazione: Caricare e associare certificati SSL nel Servizio app di Azure

[Servizio app di Azure](overview.md) offre un servizio di hosting Web con scalabilità elevata e funzioni di auto-correzione. Questa esercitazione illustra come proteggere un dominio personalizzato nel Servizio app con un certificato acquistato da un'autorità di certificazione attendibile. Mostra anche come caricare tutti i certificati privati e pubblici richiesti dall'app. Al termine, si sarà in grado di accedere all'app dall'endpoint HTTPS del dominio DNS personalizzato.

![App Web con certificato SSL personalizzato](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Aggiornare il piano tariffario dell'app
> * Proteggere un dominio personalizzato con un certificato
> * Caricare un certificato privato
> * Caricare un certificato pubblico
> * Rinnovare i certificati
> * Applicare HTTPS
> * Applicare TLS 1.1/1.2
> * Automatizzare la gestione TLS con script

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione:

- [Creare un'app del Servizio app](/azure/app-service/)
- [Eseguire il mapping di un nome DNS personalizzato all'app del Servizio app](app-service-web-tutorial-custom-domain.md) (se si protegge un dominio personalizzato)
- Acquisire un certificato da un'autorità di certificazione attendibile
- Disporre della chiave privata usata per firmare la richiesta di certificato (per i certificati privati)

<a name="requirements"></a>

## <a name="prepare-a-private-certificate"></a>Preparare un certificato privato

Per proteggere un dominio, il certificato deve soddisfare tutti i requisiti seguenti:

* Deve essere configurato per Autenticazione server
* Deve essere firmato da un'autorità di certificazione attendibile
* Deve essere esportato come file PFX protetto da password
* Deve contenere una chiave privata costituita da almeno 2048 bit
* Deve contenere tutti i certificati intermedi nella catena di certificati.

> [!TIP]
> Se è necessario un certificato SSL personalizzato, è possibile ottenerlo direttamente nel portale di Azure e importarlo nell'app. Seguire l'[esercitazione sui certificati del Servizio app](web-sites-purchase-ssl-web-site.md).

> [!NOTE]
> Sebbene non siano descritti in questo articolo, i **certificati di crittografia a curva ellittica (ECC)** possono essere usati con il servizio app. Per informazioni sulla procedura per creare i certificati ECC, rivolgersi all'autorità di certificazione.

Dopo aver ottenuto un certificato dal provider di certificati, seguire i passaggi descritti in questa sezione per prepararlo per il Servizio app.

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

A questo punto si è pronti per caricare il certificato nel Servizio app.

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

<a name="upload"></a>

## <a name="secure-a-custom-domain"></a>Proteggere un dominio personalizzato

> [!TIP]
> Se è necessario un certificato SSL personalizzato, è possibile ottenerlo direttamente nel portale di Azure e associarlo all'app. Seguire l'[esercitazione sui certificati del Servizio app](web-sites-purchase-ssl-web-site.md).

Per proteggere un [dominio personalizzato](app-service-web-tutorial-custom-domain.md) con un certificato di terze parti, si deve caricare il [certificato privato preparato](#prepare-a-private-certificate) e quindi associarlo al dominio personalizzato, ma grazie al Servizio app questo processo risulta semplificato. Seguire anche questa procedura:

Fare clic su **Domini personalizzati** nel riquadro di spostamento sinistro dell'app e quindi fare clic su **Aggiungi binding** per il dominio che si vuole proteggere. Se l'opzione **Aggiungi binding** non è visualizzata, il dominio è già protetto e dovrebbe essere contraddistinto dallo stato SSL **Protetto**.

![Aggiungere il binding al dominio](./media/app-service-web-tutorial-custom-ssl/secure-domain-launch.png)

Fare clic su **Carica certificato**.

In **File del certificato PFX** selezionare il file PFX. In **Password certificato** digitare la password creata durante l'esportazione del file PFX.

Fare clic su **Carica**.

![Caricare il certificato per il dominio](./media/app-service-web-tutorial-custom-ssl/secure-domain-upload.png)

Attendere che Azure carichi il certificato e avvii la finestra di dialogo Associazioni SSL.

Nella finestra di dialogo Associazioni SSL selezionare il certificato caricato e il tipo SSL e quindi fare clic su **Aggiungi binding**.

> [!NOTE]
> Sono supportati i tipi SSL seguenti:
>
> - **[SSL basato su SNI](https://en.wikipedia.org/wiki/Server_Name_Indication)** : è possibile aggiungere più associazioni SSL basate su SNI. Questa opzione consente di usare più certificati SSL per proteggere più domini nello stesso indirizzo IP. La maggior parte dei browser moderni (tra cui Internet Explorer, Chrome, Firefox e Opera) supporta SNI. Per altre informazioni sul supporto dei browser, vedere [Indicazione nome server](https://wikipedia.org/wiki/Server_Name_Indication).
> - **SSL basato su IP**: è possibile aggiungere una sola associazione SSL basata su IP. Questa opzione consente di usare solo un certificato SSL per proteggere un indirizzo IP pubblico dedicato. Per proteggere più domini, è necessario proteggerli tutti usando lo stesso certificato SSL. Questa è l'opzione tradizionale per l'associazione SSL.

![Associare SSL al dominio](./media/app-service-web-tutorial-custom-ssl/secure-domain-bind.png)

Lo stato SSL del dominio ora deve essere impostato su **Protetto**.

![Dominio protetto](./media/app-service-web-tutorial-custom-ssl/secure-domain-finished.png)

> [!NOTE]
> Lo stato **Protetto** nei **domini personalizzati** indica che i domini sono protetti con un certificato, ma, ad esempio, il Servizio app non verifica se il certificato è autofirmato o scaduto e questo può causare la visualizzazione di un errore o di un avviso nei browser.

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

## <a name="renew-certificates"></a>Rinnovare i certificati

L'indirizzo IP in ingresso può essere modificato quando si elimina un'associazione, anche se tale associazione è basata su IP. Questo aspetto è particolarmente importante quando si rinnova un certificato che si trova già in un'associazione basata su IP. Per evitare una modifica dell'indirizzo IP dell'app, seguire questi passaggi nell'ordine indicato:

1. Caricare il nuovo certificato.
2. Associare il nuovo certificato al dominio personalizzato desiderato senza eliminare quello precedente. Questa operazione sostituisce l'associazione anziché rimuovere quella precedente.
3. Eliminare il certificato precedente. 

<a name="bkmk_enforce"></a>

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

```azurecli-interactive
thumbprint=$(az webapp config ssl upload \
    --name <app-name> \
    --resource-group <resource-group-name> \
    --certificate-file <path-to-PFX-file> \
    --certificate-password <PFX-password> \
    --query thumbprint \
    --output tsv)
```

Il comando seguente aggiunge un'associazione SSL basata su SNI, usando l'identificazione personale ottenuta con il comando precedente.

```azurecli-interactive
az webapp config ssl bind \
    --name <app-name> \
    --resource-group <resource-group-name>
    --certificate-thumbprint $thumbprint \
    --ssl-type SNI \
```

Il comando seguente consente di applicare almeno la versione 1.2 di TLS

```azurecli-interactive
az webapp config set \
    --name <app-name> \
    --resource-group <resource-group-name>
    --min-tls-version 1.2
```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Il comando seguente carica un file PFX esportato e aggiunge un'associazione SSL basata su SNI.

```powershell
New-AzWebAppSSLBinding `
    -WebAppName <app_name> `
    -ResourceGroupName <resource_group_name> `
    -Name <dns_name> `
    -CertificateFilePath <path_to_PFX_file> `
    -CertificatePassword <PFX_password> `
    -SslState SniEnabled
```

## <a name="use-certificates-in-your-code"></a>Usare i certificati nel codice

Se l'app deve connettersi alle risorse remote e la risorsa remota richiede l'autenticazione del certificato, è possibile caricare certificati pubblici e privati nell'app. Non è necessario associare questi certificati a tutti i domini personalizzati nell'app. Per altre informazioni, vedere l'articolo relativo all'[utilizzo di un certificato SSL nel codice dell'applicazione nel servizio app di Azure](app-service-web-ssl-cert-load.md).

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
