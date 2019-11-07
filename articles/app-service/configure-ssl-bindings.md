---
title: Proteggere un nome DNS personalizzato con un'associazione SSL - Servizio app di Azure | Microsoft Docs
description: Informazioni su come acquistare un certificato del servizio app e associarlo all'app del servizio app
services: app-service
author: cephalin
manager: gwallace
tags: buy-ssl-certificates
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 10/25/2019
ms.author: cephalin
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 259a4d33ba6e8c072f8df906da4784119b299822
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73509055"
---
# <a name="secure-a-custom-dns-name-with-an-ssl-binding-in-azure-app-service"></a>Proteggere un nome DNS personalizzato con un'associazione SSL nel Servizio app di Azure

Questo articolo illustra come proteggere il [dominio personalizzato](app-service-web-tutorial-custom-domain.md) nel [Servizio app di Azure](https://docs.microsoft.com/azure/app-service/) o [app per le funzioni](https://docs.microsoft.com/azure/azure-functions/) creando un'associazione di certificati. Al termine, sarà possibile accedere Servizio app di Azure all'endpoint `https://` per nome DNS personalizzato (ad esempio `https://www.contoso.com`). 

![App Web con certificato SSL personalizzato](./media/configure-ssl-bindings/app-with-custom-ssl.png)

La protezione di un [dominio personalizzato](app-service-web-tutorial-custom-domain.md) con un certificato implica due passaggi:

- [Aggiungere un certificato privato a Servizio app di Azure](configure-ssl-certificate.md) che soddisfi tutti i requisiti per le [associazioni SSL](configure-ssl-certificate.md#private-certificate-requirements).
-  Creare un'associazione SSL al dominio personalizzato corrispondente. Questo articolo tratta proprio questo secondo passaggio.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Aggiornare il piano tariffario dell'app
> * Proteggere un dominio personalizzato con un certificato
> * Applicare HTTPS
> * Applicare TLS 1.1/1.2
> * Automatizzare la gestione TLS con script

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida pratica:

- [Creare un'app del Servizio app](/azure/app-service/)
- [Eseguire il mapping di un nome di dominio all'app](app-service-web-tutorial-custom-domain.md) o [acquistarlo e configurarlo in Azure](manage-custom-dns-buy-domain.md)
- [Aggiungere un certificato privato all'app](configure-ssl-certificate.md)

> [!NOTE]
> Il modo più semplice per aggiungere un certificato privato consiste nel [creare un certificato gestito di Servizio app di Azure](configure-ssl-certificate.md#create-a-free-certificate-preview) (anteprima).

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

<a name="upload"></a>

## <a name="secure-a-custom-domain"></a>Proteggere un dominio personalizzato

Seguire anche questa procedura:

Nel menu a sinistra nel <a href="https://portal.azure.com" target="_blank">portale di Azure</a> scegliere **Servizi app** >  **\<nome app>** .

Dalla barra di spostamento a sinistra dell'app avviare la finestra di dialogo **Associazione TLS/SSL** tramite:

- Selezione di **Domini personalizzati** > **Aggiungi associazione**
- Selezione di **Impostazioni TLS/SSL** > **Aggiungi associazione TLS/SSL**

![Aggiungere il binding al dominio](./media/configure-ssl-bindings/secure-domain-launch.png)

In **Dominio personalizzato** selezionare il dominio personalizzato a cui si vuole aggiungere un'associazione.

Se l'app dispone già di un certificato per il dominio personalizzato selezionato, passare direttamente a [Crea associazione](#create-binding). In caso contrario, continuare.

### <a name="add-a-certificate-for-custom-domain"></a>Aggiungere un certificato per dominio personalizzato

Se l'app non dispone di certificato per il dominio personalizzato selezionato, sono disponibili due opzioni:

- **Carica certificato PFX**: seguire il flusso di lavoro in [Carica un certificato privato](configure-ssl-certificate.md#upload-a-private-certificate), quindi selezionare questa opzione.
- **Carica certificato PFX**: seguire il flusso di lavoro in [Importa un certificato del servizio app](configure-ssl-certificate.md#import-an-app-service-certificate), quindi selezionare questa opzione.

> [!NOTE]
> È anche possibile [creare un di certificato gratuito (anteprima)](configure-ssl-certificate.md#create-a-free-certificate-preview) o [importare un certificato Key Vault](configure-ssl-certificate.md#import-a-certificate-from-key-vault), ma è necessario eseguire questa operazione separatamente e quindi tornare alla finestra di dialogo **Associazione TLS/SSL**.

### <a name="create-binding"></a>Crea associazione

Usare la tabella seguente per informazioni sulla configurazione dell'associazione SSL nella finestra di dialogo **Associazione TSL/SSL**, quindi fare clic su **Aggiungi associazione**.

| Impostazione | DESCRIZIONE |
|-|-|
| Dominio personalizzato | Nome di dominio per cui aggiungere l'associazione SSL. |
| Identificazione personale del certificato privato | Certificato da associare. |
| Tipo TLS/SSL | <ul><li>**[SNI SSL](https://en.wikipedia.org/wiki/Server_Name_Indication)** : è possibile aggiungere più associazioni SSL SNI. Questa opzione consente di usare più certificati SSL per proteggere più domini nello stesso indirizzo IP. La maggior parte dei browser moderni (tra cui Internet Explorer, Chrome, Firefox e Opera) supporta SNI (per altre informazioni vedere [Indicazione nome server](https://wikipedia.org/wiki/Server_Name_Indication)).</li><li>**IP SSL**: è possibile aggiungere una sola associazione IP SSL. Questa opzione consente di usare solo un certificato SSL per proteggere un indirizzo IP pubblico dedicato. Dopo aver configurato l'associazione, seguire i passaggi descritti in [Eseguire un nuovo mapping di un record per IP SSL](#remap-a-record-for-ip-ssl).<br/>IP SSL è supportato solo nei livelli Produzione o Isolato. </li></ul> |

Al termine dell'operazione, lo stato SSL del dominio personalizzato viene modificato in **Protetto**.

![Associazione SSL completata](./media/configure-ssl-bindings/secure-domain-finished.png)

> [!NOTE]
> Lo stato **Protetto** nei **domini personalizzati** indica che i domini sono protetti con un certificato, ma, ad esempio, il Servizio app non verifica se il certificato è autofirmato o scaduto e questo può causare la visualizzazione di un errore o di un avviso nei browser.

## <a name="remap-a-record-for-ip-ssl"></a>Eseguire nuovamente il mapping di un record A per IP SSL

Se non si usa il protocollo IP SSL nell'app, passare alla sezione [Testare HTTPS per il dominio personalizzato](#test-https).

Per impostazione predefinita, l'app usa un indirizzo IP pubblico condiviso. Quando si associa un certificato con il protocollo IP SSL, il servizio app crea un nuovo indirizzo IP dedicato per l'app.

Se si è eseguito il mapping di un record A all'app, aggiornare il Registro di sistema del dominio con questo nuovo indirizzo IP dedicato.

La pagina **Dominio personalizzato** dell'app viene aggiornata con il nuovo indirizzo IP dedicato. [Copiare questo indirizzo IP](app-service-web-tutorial-custom-domain.md#info), quindi [eseguire nuovamente il mapping del record A](app-service-web-tutorial-custom-domain.md#map-an-a-record) a questo indirizzo IP.

## <a name="test-https"></a>Testare HTTPS

In diversi browser passare a `https://<your.custom.domain>` per verificare che l'app sia gestita.

![Passaggio all'app di Azure nel portale](./media/configure-ssl-bindings/app-with-custom-ssl.png)

> [!NOTE]
> Se l'app restituisce errori di convalida del certificato, è probabile che sia in uso un certificato autofirmato.
>
> Se non è questo il motivo, è possibile che siano stati esclusi i certificati intermedi durante l'esportazione del certificato nel file PFX.

## <a name="prevent-ip-changes"></a>Impedire modifiche IP

L'indirizzo IP in ingresso può essere modificato quando si elimina un'associazione, anche se tale associazione è IP SSL. Questo aspetto è particolarmente importante quando si rinnova un certificato che si trova già in un'associazione IP SSL. Per evitare una modifica dell'indirizzo IP dell'app, seguire questi passaggi nell'ordine indicato:

1. Caricare il nuovo certificato.
2. Associare il nuovo certificato al dominio personalizzato desiderato senza eliminare quello precedente. Questa operazione sostituisce l'associazione anziché rimuovere quella precedente.
3. Eliminare il certificato precedente. 

## <a name="enforce-https"></a>Applicare HTTPS

Per impostazione predefinita, gli utenti possono continuare ad accedere all'app usando il protocollo HTTP. È possibile reindirizzare tutte le richieste HTTP alla porta HTTPS.

Nel riquadro di spostamento a sinistra della pagina dell'app selezionare **Impostazioni SSL**. Quindi in **Solo HTTPS**, selezionare **Attiva**.

![Applicare HTTPS](./media/configure-ssl-bindings/enforce-https.png)

Al termine dell'operazione, scegliere uno degli URL HTTP che fanno riferimento all'applicazione. Ad esempio:

- `http://<app_name>.azurewebsites.net`
- `http://contoso.com`
- `http://www.contoso.com`

## <a name="enforce-tls-versions"></a>Applicare versioni di TLS

L'app consente [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.2 per impostazione predefinita, che è il livello TLS consigliato dagli standard di settore, ad esempio [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). Per applicare versioni di TLS diverse, seguire questa procedura:

Nel riquadro di spostamento a sinistra della pagina dell'app selezionare **Impostazioni SSL**. In **TLS version** (Versione TLS) selezionare la versione minima di TLS da usare. Questa impostazione controlla solo le chiamate in ingresso. 

![Applicare TLS 1.1 o 1.2](./media/configure-ssl-bindings/enforce-tls1-2.png)

Al termine dell'operazione, l'app rifiuta tutte le connessioni con versioni di TLS meno recenti.

## <a name="automate-with-scripts"></a>Automatizzazione con gli script

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="more-resources"></a>Altre risorse

* [Usare un certificato SSL nel codice dell'applicazione](configure-ssl-certificate-in-code.md)
* [FAQ: App Service Certificates](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/) (Domande frequenti: certificati del servizio app)
