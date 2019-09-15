---
title: Acquistare e configurare un certificato SSL da Azure - Servizio app | Microsoft Docs
description: Informazioni su come acquistare un certificato del servizio app e associarlo all'app del servizio app
services: app-service
documentationcenter: .net
author: cephalin
manager: jpconnoc
tags: buy-ssl-certificates
ms.assetid: cdb9719a-c8eb-47e5-817f-e15eaea1f5f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/16/2018
ms.author: cephalin
ms.reviewer: apurvajo
ms.custom: seodec18
ms.openlocfilehash: 7c899bae6cf36e68664a3ce60939f72a4b5bd1ab
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2019
ms.locfileid: "71001215"
---
# <a name="buy-and-configure-an-ssl-certificate-for-azure-app-service"></a>Acquistare e configurare un certificato SSL per il servizio app di Azure

Questa esercitazione descrive come proteggere l'[app del servizio Web](https://docs.microsoft.com/azure/app-service/) o l'[app per le funzioni](https://docs.microsoft.com/azure/azure-functions/) tramite la creazione (acquisto) di un certificato del servizio app in [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) e la successiva associazione a un'app del servizio app.

> [!TIP]
> I certificati del servizio app possono essere usati sia per i servizi di Azure che per i servizi non di Azure e non includono solo i servizi app. A questo scopo, è necessario creare una copia PFX locale di un certificato del servizio app per poterlo usare ovunque. Per altre informazioni, vedere [Creating a local PFX copy of an App Service Certificate](https://blogs.msdn.microsoft.com/benjaminperkins/2017/04/12/export-an-azure-app-service-certificate-pfx-powershell/) (Creazione di una copia PFX locale del certificato del servizio app).
>

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida pratica:

- [Creare un'app del Servizio app](/azure/app-service/)
- [Eseguire il mapping di un nome di dominio all'app](app-service-web-tutorial-custom-domain.md) o [acquistarlo e configurarlo in Azure](manage-custom-dns-buy-domain.md)

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

## <a name="start-certificate-order"></a>Avvio dell'ordine del certificato

Avviare un ordine di un certificato del servizio app nella <a href="https://portal.azure.com/#create/Microsoft.SSL" target="_blank">pagina di creazione del certificato del servizio app</a>.

![Creazione del certificato](./media/app-service-web-purchase-ssl-web-site/createssl.png)

Usare la tabella seguente per informazioni sulla configurazione del certificato. Al termine, fare clic su **Crea**.

| Impostazione | DESCRIZIONE |
|-|-|
| Name | Nome descrittivo per il certificato del servizio app. |
| Nome host di dominio di tipo naked | Specificare qui il dominio radice. Il certificato emesso protegge *sia* il dominio radice che il `www` sottodominio. Nel certificato emesso il campo nome comune contiene il dominio radice e il campo nome alternativo oggetto contiene il `www` dominio. Per proteggere solo un sottodominio, specificare qui il nome di dominio completo del sottodominio, ad esempio `mysubdomain.contoso.com`.|
| Sottoscrizione | Data center in cui è ospitata l'app Web. |
| Gruppo di risorse | Gruppo di risorse che contiene il certificato. È possibile usare un nuovo gruppo di risorse o selezionare lo stesso gruppo di risorse, ad esempio, dell'app del servizio app. |
| SKU del certificato | Determina il tipo di certificato da creare, se si tratta di un certificato standard o di un [certificato con caratteri jolly](https://wikipedia.org/wiki/Wildcard_certificate). |
| Note legali | Fare clic per confermare che si accettano le condizioni legali. I certificati vengono ottenuti da GoDaddy. |

## <a name="store-in-azure-key-vault"></a>Archiviare il certificato in Azure Key Vault

Al termine del processo di acquisto del certificato, è necessario completare alcuni passaggi aggiuntivi prima di potere iniziare a usarlo. 

Selezionare il certificato nella pagina [Certificati del servizio app](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) e quindi fare clic su **Configurazione certificato** > **Passaggio 1: archiviare**.

![inserimento immagine stato di pronto per l'archiviazione in un insieme di credenziali delle chiavi](./media/app-service-web-purchase-ssl-web-site/ReadyKV.png)

Il [Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) è un servizio di Azure che consente di proteggere le chiavi e i segreti di crittografia usati da servizi e applicazioni cloud. È l'archiviazione scelta per i certificati del servizio app.

Nella pagina **Stato insieme di credenziali delle chiavi** fare clic su **Repository dell'insieme di credenziali delle chiavi** per creare un nuovo insieme di credenziali o sceglierne uno esistente. Se si sceglie di creare un nuovo insieme di credenziali, usare la tabella seguente per configurarlo e quindi fare clic su Crea. Vedere le informazioni per creare un nuovo insieme di credenziali delle chiavi all'interno della stessa sottoscrizione e dello stesso gruppo di risorse.

| Impostazione | DESCRIZIONE |
|-|-|
| Name | Nome univoco costituito da caratteri alfanumerici e trattini. |
| Gruppo di risorse | È consigliabile selezionare lo stesso gruppo di risorse del certificato del servizio app. |
| Location | Selezionare la stessa località dell'app del servizio app. |
| Piano tariffario | Per altre informazioni, vedere [Prezzi di Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/). |
| Criteri di accesso| Definisce le applicazioni e l'accesso consentito alle risorse dell'insieme di credenziali. È possibile configurare questa impostazione in un secondo momento, seguendo i passaggi descritti in [Concedere a diverse applicazioni l'autorizzazione per accedere a un insieme di credenziali delle chiavi](../key-vault/key-vault-group-permissions-for-apps.md). |
| Accesso alla rete virtuale | Limitare l'accesso all'insieme di credenziali a determinate reti virtuali di Azure. È possibile configurare questa impostazione in un secondo momento, seguendo i passaggi descritti in [Configurare reti virtuali e firewall di Azure Key Vault](../key-vault/key-vault-network-security.md) |

Dopo aver selezionato l'insieme di credenziali, chiudere la pagina **Repository dell'insieme di credenziali delle chiavi**. L'opzione di **archiviazione** dovrebbe mostrare un segno di spunta verde, a indicare che l'operazione è riuscita. Mantenere aperta la pagina per proseguire con il passaggio successivo.

## <a name="verify-domain-ownership"></a>Verificare la proprietà del dominio

Nella stessa pagina **Configurazione certificato** usata nell'ultimo passaggio fare clic su **Passaggio 2: verificare**.

![](./media/app-service-web-purchase-ssl-web-site/verify-domain.png)

Selezionare **Verifica del servizio app**. Poiché è già stato eseguito il mapping del dominio all'app Web (vedere [Prerequisiti](#prerequisites)), è già stato verificato. Fare semplicemente clic su **Verifica** per completare questo passaggio. Fare clic sul pulsante **Aggiorna** finché non viene visualizzato il messaggio **Il dominio del certificato è stato verificato**.

> [!NOTE]
> Sono supportati quattro tipi di metodi di verifica del dominio: 
> 
> - **Servizio app**: opzione più pratica quando è già stato eseguito il mapping del dominio a un'app del servizio app nella stessa sottoscrizione. in quanto l'app del servizio app ha già verificato la proprietà del dominio.
> - **Dominio**: verificare [un dominio del servizio app acquistato da Azure](manage-custom-dns-buy-domain.md). Azure aggiunge automaticamente il record TXT di verifica e completa il processo.
> - **Posta**: verificare il dominio inviando un messaggio di posta elettronica all'amministratore di dominio. Quando si seleziona questa opzione, vengono fornite istruzioni.
> - **Manuale**: verificare il dominio usando una pagina HTML (solo per un certificato **Standard**) o un record TXT DNS. Quando si seleziona questa opzione, vengono fornite istruzioni.

## <a name="bind-certificate-to-app"></a>Associare un certificato all'app

Nel menu a sinistra nel **[portale di Azure](https://portal.azure.com/)** scegliere **Servizi app** >  **\<app>** .

Dalla barra di spostamento a sinistra dell'app selezionare **impostazioni SSL** > **Certificati privati (.pfx)**  > **Importa il certificato del servizio app**.

![inserimento immagine dell'importazione del certificato](./media/app-service-web-purchase-ssl-web-site/ImportCertificate.png)

Selezionare il certificato appena acquistato.

Ora che il certificato è stato importato, è necessario associarlo a un nome di dominio mappato nell'app. Selezionare **Associazioni** > **Aggiungi associazione SSL**. 

![inserimento immagine dell'importazione del certificato](./media/app-service-web-purchase-ssl-web-site/AddBinding.png)

Usare la tabella seguente per informazioni sulla configurazione dell'associazione nella finestra di dialogo **Associazioni SSL**, quindi fare clic su **Aggiungi l'associazione**.

| Impostazione | Descrizione |
|-|-|
| Nome host | Nome di dominio per cui aggiungere l'associazione SSL. |
| Identificazione personale del certificato privato | Certificato da associare. |
| Tipo SSL | <ul><li>**SNI SSL**: è possibile aggiungere più associazioni SSL basate su SNI. Questa opzione consente di usare più certificati SSL per proteggere più domini nello stesso indirizzo IP. La maggior parte dei browser moderni (tra cui Internet Explorer, Chrome, Firefox e Opera) supporta SNI. Per altre informazioni sul supporto dei browser, vedere [Indicazione nome server](https://wikipedia.org/wiki/Server_Name_Indication).</li><li>**SSL basato su IP**: è possibile aggiungere una sola associazione SSL basata su IP. Questa opzione consente di usare solo un certificato SSL per proteggere un indirizzo IP pubblico dedicato. Dopo aver configurato l'associazione, seguire i passaggi descritti in [Eseguire un nuovo mapping di un record per IP SSL](app-service-web-tutorial-custom-ssl.md#remap-a-record-for-ip-ssl). </li></ul> |

## <a name="verify-https-access"></a>Verificare l'accesso HTTPS

Visitare l'app usando `HTTPS://<domain_name>` invece di `HTTP://<domain_name>` per verificare che il certificato sia stato configurato correttamente.

## <a name="rekey-certificate"></a>Reimposta chiavi certificato

Se si ritiene che la chiave privata del certificato venga compromessa, è possibile rekey il certificato. Selezionare il certificato nella pagina [certificati del servizio app](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) , quindi selezionare **rekey e sincronizza** dal percorso di spostamento a sinistra.

Fare clic su **rekey** per avviare il processo. Questo processo può richiedere da 1 a 10 minuti.

![inserire immagine della reimpostazione SSL](./media/app-service-web-purchase-ssl-web-site/Rekey.png)

Con la reimpostazione viene emesso un nuovo certificato da parte dell'autorità di certificazione.

Al termine dell'operazione rekey, fare clic su **Sincronizza**. L'operazione di sincronizzazione aggiorna automaticamente le associazioni del nome host per il certificato nel servizio app senza causare tempi di inattività per le app.

> [!NOTE]
> Se non si fa clic su **Sincronizza**, il servizio app sincronizza automaticamente il certificato entro 48 ore.

## <a name="renew-certificate"></a>Rinnova certificato

Per attivare il rinnovo automatico del certificato in qualsiasi momento, selezionare il certificato nella pagina [Certificati del servizio app](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) e quindi fare clic su **Impostazioni di rinnovo automatico** nel riquadro di spostamento a sinistra. Per impostazione predefinita, i certificati del servizio app hanno un periodo di validità di 1 anno.

Selezionare **Attivato** e fare clic su **Salva**. Il rinnovo dei certificati può essere avviato automaticamente 60 giorni prima della scadenza se è attivato il rinnovo automatico.

![rinnovo automatico del certificato](./media/app-service-web-purchase-ssl-web-site/auto-renew.png)

Per rinnovare il certificato manualmente, fare clic su **Rinnovo manuale**. È possibile richiedere di rinnovare il certificato manualmente 60 giorni prima della scadenza.

Al termine dell'operazione di rinnovo, fare clic su **Sincronizza**. L'operazione di sincronizzazione aggiorna automaticamente le associazioni del nome host per il certificato nel servizio app senza causare tempi di inattività per le app.

> [!NOTE]
> Se non si fa clic su **Sincronizza**, il servizio app sincronizza automaticamente il certificato entro 48 ore.

## <a name="automate-with-scripts"></a>Automatizzazione con gli script

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="more-resources"></a>Altre risorse

* [Applicare HTTPS](app-service-web-tutorial-custom-ssl.md#enforce-https)
* [Applicare TLS 1.1/1.2](app-service-web-tutorial-custom-ssl.md#enforce-tls-versions)
* [Usare un certificato SSL nel codice dell'applicazione in Servizio app di Azure](app-service-web-ssl-cert-load.md)
* [FAQ: App Service Certificates](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/) (Domande frequenti: certificati del servizio app)
