---
title: Aggiungere e gestire i certificati SSL - Servizio app di Azure | Microsoft Docs
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
ms.openlocfilehash: 48c8390eff52466d11f781447c448d04ba567f31
ms.sourcegitcommit: 6dec090a6820fb68ac7648cf5fa4a70f45f87e1a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2019
ms.locfileid: "73907134"
---
# <a name="add-an-ssl-certificate-in-azure-app-service"></a>Aggiungere un certificato SSL al servizio app di Azure

[Servizio app di Azure](overview.md) offre un servizio di hosting Web con scalabilità elevata e funzioni di auto-correzione. Questo articolo illustra come creare, caricare o importare un certificato privato o un certificato pubblico nel servizio app. 

Dopo aver aggiunto il certificato all'app del servizio app o all'[app per le funzioni](https://docs.microsoft.com/azure/azure-functions/), è possibile [proteggere un nome DNS personalizzato con tale certificato](configure-ssl-bindings.md) o [usarlo nel codice dell'applicazione](configure-ssl-certificate-in-code.md).

La tabella seguente elenca le opzioni disponibili per aggiungere certificati nel servizio app:

|Opzione|DESCRIZIONE|
|-|-|
| Creare un certificato gratuito gestito dal servizio app (anteprima) | Un certificato privato facile da usare se è sufficiente proteggere il proprio [dominio personalizzato](app-service-web-tutorial-custom-domain.md) `www` o qualsiasi dominio non di tipo naked nel servizio app. |
| Acquistare un certificato del servizio app | Un certificato privato gestito da Azure. Combina la semplicità della gestione automatica dei certificati e la flessibilità delle opzioni di rinnovo ed esportazione. |
| Importare un certificato da Key Vault | Utile se si usa [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) per gestire i [certificati PKCS12](https://wikipedia.org/wiki/PKCS_12). Vedere [Requisiti dei certificati privati](#private-certificate-requirements). |
| Caricare un certificato privato | Se si ha già un certificato privato rilasciato da un provider di terze parti, è possibile caricarlo. Vedere [Requisiti dei certificati privati](#private-certificate-requirements). |
| Caricare un certificato pubblico | I certificati pubblici non vengono usati per proteggere i domini personalizzati, ma è possibile caricarli nel codice se sono necessari per accedere a risorse remote. |

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida pratica:

- [Creare un'app del servizio app](/azure/app-service/).
- Solo per i certificati gratuiti: eseguire il mapping di un sottodominio, ad esempio `www.contoso.com`, al servizio app con un [record CNAME](app-service-web-tutorial-custom-domain.md#map-a-cname-record).

## <a name="private-certificate-requirements"></a>Requisiti dei certificati privati

Il [certificato gratuito gestito dal servizio app](#create-a-free-certificate-preview) o il [certificato del servizio app](#import-an-app-service-certificate) soddisfano già i requisiti del servizio. Se si sceglie di caricare o importare un certificato privato nel servizio app, il certificato deve soddisfare i requisiti seguenti:

* Deve essere esportato come [file PFX protetto da password](https://en.wikipedia.org/w/index.php?title=X.509&section=4#Certificate_filename_extensions)
* Deve contenere una chiave privata costituita da almeno 2048 bit
* Deve contenere tutti i certificati intermedi nella catena di certificati.

Per proteggere un dominio personalizzato in un'associazione SSL, il certificato presenta requisiti aggiuntivi:

* Contiene un'estensione di [utilizzo chiavi avanzato](https://en.wikipedia.org/w/index.php?title=X.509&section=4#Extensions_informing_a_specific_usage_of_a_certificate) per l'autenticazione server (OID = 1.3.6.1.5.5.7.3.1)
* Deve essere firmato da un'autorità di certificazione attendibile

> [!NOTE]
> Sebbene non siano descritti in questo articolo, i **certificati di crittografia a curva ellittica (ECC)** possono essere usati con il servizio app. Per informazioni sulla procedura per creare i certificati ECC, rivolgersi all'autorità di certificazione.

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

## <a name="create-a-free-certificate-preview"></a>Creare un certificato gratuito (anteprima)

Il certificato gratuito gestito dal servizio app è una soluzione rapida ed efficace per proteggere il proprio nome DNS personalizzato nel servizio app. Si tratta di un certificato SSL completamente funzionante che viene gestito dal servizio app e rinnovato automaticamente. Il certificato gratuito presenta le limitazioni seguenti:

- Non supporta i certificati con caratteri jolly.
- Non supporta i domini di tipo naked.
- Non è esportabile.

> [!NOTE]
> Il certificato gratuito viene rilasciato da DigiCert. Per alcuni domini di primo livello, è necessario consentire in modo esplicito a DigiCert di agire come autorità di certificazione creando un [record di dominio CAA](https://wikipedia.org/wiki/DNS_Certification_Authority_Authorization) con il valore seguente: `0 issue digicert.com`.
> 

Per creare un certificato gratuito gestito dal servizio app:

Nel menu a sinistra del <a href="https://portal.azure.com" target="_blank">portale di Azure</a> scegliere **Servizi app** >  **\<nome app>** .

Nel riquadro di spostamento a sinistra dell'app selezionare **Impostazioni TLS/SSL** > **Certificati a chiave privata (.pfx)**  > **Crea certificato gestito dal servizio app**.

![Creare il certificato gratuito nel servizio app](./media/configure-ssl-certificate/create-free-cert.png)

Nella finestra di dialogo sono elencati tutti i domini non di tipo naked mappati correttamente all'app con un record CNAME. Selezionare il dominio personalizzato per il quale creare un certificato gratuito e scegliere **Crea**. È possibile creare un solo certificato per ogni dominio personalizzato supportato.

Al termine dell'operazione, il certificato viene visualizzato nell'elenco **Certificati a chiave privata**.

![Creazione del certificato gratuito completata](./media/configure-ssl-certificate/create-free-cert-finished.png)

> [!IMPORTANT] 
> Per proteggere un dominio personalizzato con questo certificato, è necessario anche creare un'associazione di certificato. Seguire la procedura descritta in [Creare l'associazione](configure-ssl-bindings.md#create-binding).
>

## <a name="import-an-app-service-certificate"></a>Importare un certificato del servizio app

Se si acquista un certificato del servizio app da Azure, Azure gestisce le attività seguenti:

- Si occupa del processo di acquisto da GoDaddy.
- Esegue la verifica del dominio del certificato.
- Gestisce il certificato in [Azure Key Vault](../key-vault/key-vault-overview.md).
- Gestisce il rinnovo del certificato (vedere [Rinnovare il certificato](#renew-certificate)).
- Sincronizza automaticamente il certificato con le copie importate nelle app del servizio app.

Per acquistare un certificato del servizio app, passare ad [Avvio dell'ordine del certificato](#start-certificate-order).

Se si ha già un certificato del servizio app funzionante, è possibile:

- [Importare il certificato nel servizio app](#import-certificate-into-app-service).
- [Gestire il certificato](#manage-app-service-certificates), ad esempio rinnovarlo, reimpostare la chiave ed esportarlo.

### <a name="start-certificate-order"></a>Avvio dell'ordine del certificato

Avviare un ordine di un certificato del servizio app nella <a href="https://portal.azure.com/#create/Microsoft.SSL" target="_blank">pagina di creazione del certificato del servizio app</a>.

![Avviare l'acquisto del certificato del servizio app](./media/configure-ssl-certificate/purchase-app-service-cert.png)

Usare la tabella seguente per informazioni sulla configurazione del certificato. Al termine, fare clic su **Crea**.

| Impostazione | DESCRIZIONE |
|-|-|
| NOME | Nome descrittivo per il certificato del servizio app. |
| Nome host di dominio di tipo naked | Specificare qui il dominio radice. Il certificato emesso protegge *sia* il dominio radice sia il sottodominio `www`. Nel certificato emesso, il campo relativo al nome comune contiene il dominio radice e quello relativo al nome alternativo del soggetto contiene il dominio `www`. Per proteggere solo un sottodominio, specificare qui il nome di dominio completo del sottodominio, ad esempio `mysubdomain.contoso.com`.|
| Subscription | Data center in cui è ospitata l'app Web. |
| Resource group | Gruppo di risorse che contiene il certificato. È possibile usare un nuovo gruppo di risorse o selezionare lo stesso gruppo di risorse, ad esempio, dell'app del servizio app. |
| Certificato SKU | Determina il tipo di certificato da creare, se si tratta di un certificato standard o di un [certificato con caratteri jolly](https://wikipedia.org/wiki/Wildcard_certificate). |
| Note legali | Fare clic per confermare che si accettano le condizioni legali. I certificati vengono ottenuti da GoDaddy. |

### <a name="store-in-azure-key-vault"></a>Archiviare il certificato in Azure Key Vault

Al termine del processo di acquisto del certificato, è necessario completare alcuni passaggi aggiuntivi prima di potere iniziare a usarlo. 

Selezionare il certificato nella pagina [Certificati del servizio app](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) e quindi fare clic su **Configurazione certificato** > **Passaggio 1: archiviare**.

![Configurare l'archiviazione Key Vault del certificato del servizio app](./media/configure-ssl-certificate/configure-key-vault.png)

Il [Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) è un servizio di Azure che consente di proteggere le chiavi e i segreti di crittografia usati da servizi e applicazioni cloud. È l'archiviazione scelta per i certificati del servizio app.

Nella pagina **Stato insieme di credenziali delle chiavi** fare clic su **Repository dell'insieme di credenziali delle chiavi** per creare un nuovo insieme di credenziali o sceglierne uno esistente. Se si sceglie di creare un nuovo insieme di credenziali, usare la tabella seguente per configurarlo e quindi fare clic su Crea. Vedere le informazioni per creare un nuovo insieme di credenziali delle chiavi all'interno della stessa sottoscrizione e dello stesso gruppo di risorse.

| Impostazione | DESCRIZIONE |
|-|-|
| NOME | Nome univoco costituito da caratteri alfanumerici e trattini. |
| Resource group | È consigliabile selezionare lo stesso gruppo di risorse del certificato del servizio app. |
| Location | Selezionare la stessa località dell'app del servizio app. |
| Piano tariffario | Per altre informazioni, vedere [Prezzi di Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/). |
| Criteri di accesso| Definisce le applicazioni e l'accesso consentito alle risorse dell'insieme di credenziali. È possibile configurare questa impostazione in un secondo momento, seguendo i passaggi descritti in [Concedere a diverse applicazioni l'autorizzazione per accedere a un insieme di credenziali delle chiavi](../key-vault/key-vault-group-permissions-for-apps.md). |
| Accesso alla rete virtuale | Limitare l'accesso all'insieme di credenziali a determinate reti virtuali di Azure. È possibile configurare questa impostazione in un secondo momento, seguendo i passaggi descritti in [Configurare reti virtuali e firewall di Azure Key Vault](../key-vault/key-vault-network-security.md) |

Dopo aver selezionato l'insieme di credenziali, chiudere la pagina **Repository dell'insieme di credenziali delle chiavi**. L'opzione di **archiviazione** dovrebbe mostrare un segno di spunta verde, a indicare che l'operazione è riuscita. Mantenere aperta la pagina per proseguire con il passaggio successivo.

### <a name="verify-domain-ownership"></a>Verificare la proprietà del dominio

Nella stessa pagina **Configurazione certificato** usata nell'ultimo passaggio fare clic su **Passaggio 2: verificare**.

![Verificare il dominio per il certificato del servizio app](./media/configure-ssl-certificate/verify-domain.png)

Selezionare **Verifica del servizio app**. Poiché è già stato eseguito il mapping del dominio all'app Web (vedere [Prerequisiti](#prerequisites)), è già stato verificato. Fare semplicemente clic su **Verifica** per completare questo passaggio. Fare clic sul pulsante **Aggiorna** finché non viene visualizzato il messaggio **Il dominio del certificato è stato verificato**.

> [!NOTE]
> Sono supportati quattro tipi di metodi di verifica del dominio: 
> 
> - **Servizio app**: opzione più pratica quando è già stato eseguito il mapping del dominio a un'app del servizio app nella stessa sottoscrizione. in quanto l'app del servizio app ha già verificato la proprietà del dominio.
> - **Dominio**: verificare [un dominio del servizio app acquistato da Azure](manage-custom-dns-buy-domain.md). Azure aggiunge automaticamente il record TXT di verifica e completa il processo.
> - **Posta**: verificare il dominio inviando un messaggio di posta elettronica all'amministratore di dominio. Quando si seleziona questa opzione, vengono fornite istruzioni.
> - **Manuale**: verificare il dominio usando una pagina HTML (solo per un certificato **Standard**) o un record TXT DNS. Quando si seleziona questa opzione, vengono fornite istruzioni.

### <a name="import-certificate-into-app-service"></a>Importare il certificato nel servizio app

Nel menu a sinistra del <a href="https://portal.azure.com" target="_blank">portale di Azure</a> scegliere **Servizi app** >  **\<nome app>** .

Nel riquadro di spostamento a sinistra dell'app selezionare **Impostazioni TLS/SSL** > **Certificati a chiave privata (.pfx)**  > **Importa il certificato del servizio app**.

![Importare il certificato nel servizio app](./media/configure-ssl-certificate/import-app-service-cert.png)

Selezionare il certificato appena acquistato e scegliere **OK**.

Al termine dell'operazione, il certificato viene visualizzato nell'elenco **Certificati a chiave privata**.

![Importazione del certificato del servizio app completata](./media/configure-ssl-certificate/import-app-service-cert-finished.png)

> [!IMPORTANT] 
> Per proteggere un dominio personalizzato con questo certificato, è necessario anche creare un'associazione di certificato. Seguire la procedura descritta in [Creare l'associazione](configure-ssl-bindings.md#create-binding).
>

## <a name="import-a-certificate-from-key-vault"></a>Importare un certificato da Key Vault

Se si usa Azure Key Vault per gestire i certificati, è possibile importare un certificato PKCS12 da Key Vault nel servizio app, purché [siano soddisfatti i requisiti](#private-certificate-requirements).

Nel menu a sinistra del <a href="https://portal.azure.com" target="_blank">portale di Azure</a> scegliere **Servizi app** >  **\<nome app>** .

Nel riquadro di spostamento a sinistra dell'app selezionare **Impostazioni TLS/SSL** > **Certificati a chiave privata (.pfx)**  > **Importa certificato dell'insieme di credenziali delle chiavi**.

![Importare il certificato di Key Vault nel servizio app](./media/configure-ssl-certificate/import-key-vault-cert.png))

La tabella seguente contiene informazioni utili per facilitare la selezione del certificato.

| Impostazione | DESCRIZIONE |
|-|-|
| Subscription | La sottoscrizione a cui appartiene l'istanza di Key Vault. |
| Key Vault | L'insieme di credenziali con il certificato da importare. |
| Certificate | Selezionare il certificato dall'elenco di certificati PKCS12 nell'insieme di credenziali. Tutti i certificati PKCS12 nell'insieme di credenziali sono elencati con le relative identificazioni, ma non tutti sono supportati nel servizio app. |

Al termine dell'operazione, il certificato viene visualizzato nell'elenco **Certificati a chiave privata**. Se l'importazione non riesce e viene restituito un errore, significa che il certificato non soddisfa i [requisiti per il servizio app](#private-certificate-requirements).

![Importazione del certificato di Key Vault completata](./media/configure-ssl-certificate/import-app-service-cert-finished.png)

> [!IMPORTANT] 
> Per proteggere un dominio personalizzato con questo certificato, è necessario anche creare un'associazione di certificato. Seguire la procedura descritta in [Creare l'associazione](configure-ssl-bindings.md#create-binding).
>

## <a name="upload-a-private-certificate"></a>Caricare un certificato privato

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

### <a name="upload-certificate-to-app-service"></a>Caricare il certificato nel servizio app

A questo punto si è pronti per caricare il certificato nel Servizio app.

Nel menu a sinistra del <a href="https://portal.azure.com" target="_blank">portale di Azure</a> scegliere **Servizi app** >  **\<nome app>** .

Nel riquadro di spostamento a sinistra dell'app selezionare **Impostazioni TLS/SSL** > **Certificati a chiave privata (.pfx)**  > **Carica certificato**.

![Caricare il certificato privato nel servizio app](./media/configure-ssl-certificate/upload-private-cert.png)

In **File del certificato PFX** selezionare il file PFX. In **Password certificato** digitare la password creata durante l'esportazione del file PFX. Al termine, fare clic su **Carica**. 

Al termine dell'operazione, il certificato viene visualizzato nell'elenco **Certificati a chiave privata**.

![Caricamento del certificato completato](./media/configure-ssl-certificate/create-free-cert-finished.png)

> [!IMPORTANT] 
> Per proteggere un dominio personalizzato con questo certificato, è necessario anche creare un'associazione di certificato. Seguire la procedura descritta in [Creare l'associazione](configure-ssl-bindings.md#create-binding).
>

## <a name="upload-a-public-certificate"></a>Caricare un certificato pubblico

I certificati pubblici sono supportati nel formato *.cer*. 

Nel menu a sinistra del <a href="https://portal.azure.com" target="_blank">portale di Azure</a> scegliere **Servizi app** >  **\<nome app>** .

Nel riquadro di spostamento a sinistra dell'app selezionare **Impostazioni TLS/SSL** > **Certificati pubblici (.cer)**  > **Carica certificato a chiave pubblica**.

In **Nome** digitare un nome per il certificato. In **File di certificato CER** selezionare il file CER.

Fare clic su **Carica**.

![Caricare il certificato pubblico nel servizio app](./media/configure-ssl-certificate/upload-public-cert.png)

Dopo aver caricato il certificato, copiare l'identificazione personale del certificato e vedere [Rendere accessibile il certificato](configure-ssl-certificate-in-code.md#make-the-certificate-accessible).

## <a name="manage-app-service-certificates"></a>Gestire i certificati del servizio app

Questa sezione illustra come gestire un certificato del servizio app acquistato con la procedura descritta in [Importare un certificato del servizio app](#import-an-app-service-certificate).

- [Reimpostare la chiave del certificato](#rekey-certificate)
- [Rinnovare il certificato](#renew-certificate)
- [Esportare il certificato](#export-certificate)
- [Eliminare il certificato](#delete-certificate)

### <a name="rekey-certificate"></a>Reimpostare la chiave del certificato

Se si ritiene che la chiave privata del certificato sia compromessa, è possibile reimpostarla. Selezionare il certificato nella pagina [Certificati del servizio app](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) e quindi selezionare **Reimposta chiavi e sincronizza** nel riquadro di spostamento a sinistra.

Fare clic su **Reimposta chiavi** per avviare il processo. Questo processo può richiedere da 1 a 10 minuti.

![Reimpostare la chiave di un certificato del servizio app](./media/configure-ssl-certificate/rekey-app-service-cert.png)

Con la reimpostazione viene emesso un nuovo certificato da parte dell'autorità di certificazione.

Al termine dell'operazione di reimpostazione della chiave, fare clic su **Sincronizza**. L'operazione di sincronizzazione aggiorna automaticamente le associazioni di nome host per il certificato nel servizio app senza causare tempi di inattività per le app.

> [!NOTE]
> Se non si fa clic su **Sincronizza**, il servizio app sincronizza automaticamente il certificato entro 48 ore.

### <a name="renew-certificate"></a>Certificato da rinnovare

Per attivare il rinnovo automatico del certificato in qualsiasi momento, selezionare il certificato nella pagina [Certificati del servizio app](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) e quindi fare clic su **Impostazioni di rinnovo automatico** nel riquadro di spostamento a sinistra. Per impostazione predefinita, i certificati del servizio app hanno un periodo di validità di un anno.

Selezionare **Attivato** e fare clic su **Salva**. Il rinnovo dei certificati può essere avviato automaticamente 60 giorni prima della scadenza se è attivato il rinnovo automatico.

![Rinnovare automaticamente il certificato del servizio app](./media/configure-ssl-certificate/auto-renew-app-service-cert.png)

Per rinnovare il certificato manualmente, fare clic su **Rinnovo manuale**. È possibile richiedere di rinnovare il certificato manualmente 60 giorni prima della scadenza.

Al termine dell'operazione di rinnovo, fare clic su **Sincronizza**. L'operazione di sincronizzazione aggiorna automaticamente le associazioni di nome host per il certificato nel servizio app senza causare tempi di inattività per le app.

> [!NOTE]
> Se non si fa clic su **Sincronizza**, il servizio app sincronizza automaticamente il certificato entro 48 ore.

### <a name="export-certificate"></a>Esportare il certificato

Dal momento che un certificato del servizio app è un [segreto di Key Vault](../key-vault/about-keys-secrets-and-certificates.md#key-vault-secrets), è possibile esportarne una copia in formato PFX e usarla per altri servizi di Azure o all'esterno di Azure.

Per esportare il certificato del servizio app come file PFX, eseguire i comandi seguenti in [Cloud Shell](https://shell.azure.com). È possibile eseguire questa operazione anche in locale se è stata [installata l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). Sostituire i segnaposto con i nomi usati al momento della [creazione del certificato del servizio app](#start-certificate-order).

```azurecli-interactive
secretname=$(az resource show \
    --resource-group <group-name> \
    --resource-type "Microsoft.CertificateRegistration/certificateOrders" \
    --name <app-service-cert-name> \
    --query "properties.certificates.<app-service-cert-name>.keyVaultSecretName" \
    --output tsv)

az keyvault secret download \
    --file appservicecertificate.pfx \
    --vault-name <key-vault-name> \
    --name $secretname \
    --encoding base64
```

Il file *appservicecertificate.pfx* scaricato è un file PKCS12 non elaborato che contiene certificati pubblici e privati. A ogni prompt usare una stringa vuota sia per la password di importazione che per la passphrase PEM.

### <a name="delete-certificate"></a>Eliminazione di un certificato 

L'eliminazione di un certificato del servizio app è un'operazione definitiva e irreversibile. Qualsiasi associazione a questo certificato nel servizio app diventa non valida. Per impedire l'eliminazione accidentale, Azure applica un blocco al certificato. Per eliminare un certificato del servizio app, è necessario prima rimuovere il blocco per l'eliminazione sul certificato.

Selezionare il certificato nella pagina [Certificati del servizio app](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) e quindi selezionare **Blocchi** nel riquadro di spostamento a sinistra.

Trovare il blocco di tipo **Elimina** sul certificato. A destra del blocco selezionare **Elimina**.

![Eliminare il blocco per il certificato del servizio app](./media/configure-ssl-certificate/delete-lock-app-service-cert.png)

A questo punto è possibile eliminare il certificato del servizio app. Nel riquadro di spostamento a sinistra selezionare **Panoramica** > **Elimina**. Nella finestra di dialogo di conferma digitare il nome del certificato e scegliere **OK**.

## <a name="automate-with-scripts"></a>Automatizzazione con gli script

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="more-resources"></a>Altre risorse

* [Proteggere un nome DNS personalizzato con un'associazione SSL](configure-ssl-bindings.md)
* [Applicare HTTPS](configure-ssl-bindings.md#enforce-https)
* [Applicare TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions)
* [Usare un certificato SSL nel codice dell'applicazione](configure-ssl-certificate-in-code.md)
* [FAQ: App Service Certificates](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/) (Domande frequenti: certificati del servizio app)
