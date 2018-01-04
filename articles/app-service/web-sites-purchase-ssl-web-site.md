---
title: Acquistare e configurare un certificato SSL per il servizio app di Azure | Microsoft Docs
description: Informazioni su come acquistare un certificato del servizio app e associarlo all'app del servizio app
services: app-service
documentationcenter: .net
author: cephalin
manager: cfowler
tags: buy-ssl-certificates
ms.assetid: cdb9719a-c8eb-47e5-817f-e15eaea1f5f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: apurvajo;cephalin
ms.openlocfilehash: 6c0125bf0bd22912a21372b5a7da6846e924e6cd
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2017
---
# <a name="buy-and-configure-an-ssl-certificate-for-your-azure-app-service"></a>Acquistare e configurare un certificato SSL per il servizio app di Azure

Questa esercitazione illustra come proteggere l'app Web tramite l'acquisto di un certificato SSL per il **[servizio app di Azure](http://go.microsoft.com/fwlink/?LinkId=529714)**, archiviandolo in modo sicuro in [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) e associandolo a un dominio personalizzato.

## <a name="step-1---log-in-to-azure"></a>Passaggio 1: Accedere ad Azure

Accedere al portale di Azure all'indirizzo http://portal.azure.com

## <a name="step-2---place-an-ssl-certificate-order"></a>Passaggio 2: eseguire un ordine per un certificato SSL

È possibile ordinare un certificato SSL creando un nuovo [certificato di servizio App](https://portal.azure.com/#create/Microsoft.SSL) nel **portale di Azure**.

![Creazione del certificato](./media/app-service-web-purchase-ssl-web-site/createssl.png)

Immettere un **nome descrittivo** per il certificato SSL e immettere il **nome del dominio**

> [!NOTE]
> Questo passaggio è una delle parti più importanti del processo di acquisto. Assicurarsi di immettere il nome host corretto (dominio personalizzato) che si desidera proteggere con il certificato. **NON** aggiungere WWW al nome host. 
>

Selezionare la **Sottoscrizione**, il **Gruppo di risorse** e lo **SKU del certificato**

> [!WARNING]
> I certificati del servizio App possono essere usati solo da altri servizi App nella stessa sottoscrizione.  
>

## <a name="step-3---store-the-certificate-in-azure-key-vault"></a>Passaggio 3: archiviare il certificato nell'Azure Key Vault

> [!NOTE]
> Il [Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) è un servizio di Azure che consente di proteggere le chiavi e i segreti di crittografia usati da servizi e applicazioni cloud.
>

Dopo aver completato l'acquisto del certificato SSL, è necessario aprire la pagina [Certificati del servizio app](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders).

![inserimento immagine stato di pronto per l'archiviazione in un insieme di credenziali delle chiavi](./media/app-service-web-purchase-ssl-web-site/ReadyKV.png)

Lo stato del certificato è **"In attesa di rilascio"** dato che è necessario completare alcuni altri passaggi prima di iniziare a usare questo certificato.

Fare clic su **Configurazione certificato** nella pagina Proprietà certificato e quindi fare clic su **Passaggio 1: archiviare** per archiviare il certificato in Azure Key Vault.

Nella pagina **Stato insieme di credenziali delle chiavi** fare clic su **Repository dell'insieme di credenziali delle chiavi** per scegliere un insieme esistente in cui archiviare il certificato OPPURE fare clic su **Crea nuovo insieme di credenziali delle chiavi** per creare un nuovo insieme nella stessa sottoscrizione e nello stesso gruppo di risorse.

> [!NOTE]
> L' Azure Key Vault prevede addebiti minimi per l'archiviazione di questo certificato.
> Per altre informazioni, vedere **[Prezzi di Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/)**.
>

Dopo aver selezionato l'archivio del Key Vault in cui archiviare il certificato, selezionare il pulsante **Archivia**.

![inserimento immagine per la riuscita dell'archiviazione in un Key Vault](./media/app-service-web-purchase-ssl-web-site/KVStoreSuccess.png)

## <a name="step-4---verify-the-domain-ownership"></a>Passaggio 4: verificare la proprietà del dominio

Nella stessa pagina **Configurazione certificato** usata nel passaggio 3 fare clic su **Passaggio 2: verificare**.

Scegliere il metodo di verifica del dominio preferito. 

Esistono quattro tipi di verifica del dominio supportati da Certificati del servizio app: Servizio app, Dominio, Posta e Verifica manuale. Questi tipi di verifica sono illustrati più dettagliatamente nella [sezione Avanzate](#advanced).

> [!NOTE]
> **Verifica del servizio app** è l'opzione più efficiente quando il dominio che si intende verificare è già mappato a un'app del servizio app nella stessa sottoscrizione in quanto l'app del servizio app ha già verificato la proprietà del dominio.
>

Fare clic sul pulsante **Verifica** per completare questo passaggio.

![inserimento immagine della verifica del dominio](./media/app-service-web-purchase-ssl-web-site/DomainVerificationRequired.png)

Dopo aver fatto clic su **Verifica**, utilizzare il pulsante **Aggiorna** fino a quando l'opzione **Verifica** non avrà esito positivo.

![inserimento immagine per la riuscita della verifica in un Key Vault](./media/app-service-web-purchase-ssl-web-site/KVVerifySuccess.png)

## <a name="step-5---assign-certificate-to-app-service-app"></a>Passaggio 5: Assegnare il certificato all'app del servizio app

> [!NOTE]
> Prima di eseguire la procedura inclusa in questa sezione, è necessario avere associato un nome di dominio personalizzato all'app. Per altre informazioni, vedere **[Configurare un nome di dominio personalizzato nel servizio app di Azure.](app-service-web-tutorial-custom-domain.md)**
>

Nel **[portale di Azure](https://portal.azure.com/)** fare clic sull'opzione **Servizio app** a sinistra nella pagina.

Fare clic sul nome dell'app a cui si desidera assegnare il certificato.

In **Impostazioni** fare clic su **Certificati SSL**.

Fare clic su **Importa il certificato di servizio app** e selezionare il certificato appena acquistato.

![inserimento immagine dell'importazione del certificato](./media/app-service-web-purchase-ssl-web-site/ImportCertificate.png)

Nella sezione **associazione SSL** fare clic su **Aggiungi associazioni** e usare gli elenchi a discesa per selezionare il nome di dominio da proteggere con SSL e il certificato da usare. È possibile anche stabilire se usare il metodo SSL basato su **[Indicazione nome server (SNI)](http://en.wikipedia.org/wiki/Server_Name_Indication)** o su IP.

![inserimento immagine di associazioni SSL](./media/app-service-web-purchase-ssl-web-site/SSLBindings.png)

Fare clic su **Aggiungi l'associazione** per salvare le modifiche e abilitare SSL.

> [!NOTE]
> Se è stata selezionata l'opzione **SSL basato su IP** e il dominio personalizzato è stato configurato usando un record A, sarà necessario eseguire i passaggi aggiuntivi seguenti. Sono illustrati più dettagliatamente nella [sezione Avanzata](#Advanced).

A questo punto si dovrebbe poter andare all'app usando `HTTPS://` anziché `HTTP://` per verificare che il certificato sia stato configurato correttamente.

<!--![insert image of https](./media/app-service-web-purchase-ssl-web-site/Https.png)-->

## <a name="step-6---management-tasks"></a>Passaggio 6: attività di gestione

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="advanced"></a>Avanzate

### <a name="verifying-domain-ownership"></a>Verifica della la proprietà del dominio

Esistono altri due tipi di verifica del dominio supportati da Certificati del servizio app: Mail e Manual Verification.

#### <a name="mail-verification"></a>Verifica tramite posta elettronica

Un messaggio di posta elettronica di verifica è già stato inviato agli indirizzi di posta elettronica associati al dominio personalizzato.
Per completare il passaggio di verifica tramite posta elettronica, aprire la posta elettronica e fare clic sul collegamento di verifica.

![inserimento immagine della verifica dell'indirizzo di posta elettronica](./media/app-service-web-purchase-ssl-web-site/KVVerifyEmailSuccess.png)

Se è necessario un nuovo invio del messaggio di verifica, fare clic sul pulsante **Invia di nuovo il messaggio di posta elettronica**.

#### <a name="domain-verification"></a>Verifica del dominio

Scegliere questa opzione solo per un [dominio del servizio app che è stato acquistato da Azure](custom-dns-web-site-buydomains-web-app.md). Azure aggiunge automaticamente il record TXT di verifica e completa il processo.

#### <a name="manual-verification"></a>Verifica manuale

> [!IMPORTANT]
> Verifica della pagina Web HTML (funziona solo con lo SKU del certificato standard)
>

1. Creare un file HTML denominato **"starfield.html"**

1. Il contenuto di questo file deve corrispondere esattamente al nome del token di verifica del dominio. È possibile copiare il token dalla pagina dello stato di verifica del dominio.

1. Caricare il file nella radice del server Web che ospita il dominio `/.well-known/pki-validation/starfield.html`

1. Fare clic su **Aggiorna** per aggiornare lo stato del certificato dopo aver completato la verifica. Il completamento della verifica potrebbe richiedere qualche minuto.

> [!TIP]
> Verificare in un terminale mediante `curl -G http://<domain>/.well-known/pki-validation/starfield.html` la risposta deve contenere il `<verification-token>`.

#### <a name="dns-txt-record-verification"></a>Verifica del record TXT DNS

1. Usando il gestore DNS, creare un record TXT nel sottodominio `@` con valore uguale al token di verifica del dominio.
1. Fare clic su **"Aggiorna"** per aggiornare lo stato del certificato dopo aver completato la verifica.

> [!TIP]
> È necessario creare un record TXT su `@.<domain>` con valore `<verification-token>`.

### <a name="assign-certificate-to-app-service-app"></a>Assegnare il certificato all'app del servizio app

Se è stata selezionata l'opzione **SSL basato su IP** e il dominio personalizzato è stato configurato usando un record A, sarà necessario eseguire i passaggi aggiuntivi seguenti:

Dopo la configurazione di un'associazione SSL basata su IP, un indirizzo IP dedicato viene assegnato all'app. È possibile trovare questo indirizzo IP nella pagina **Dominio personalizzato** nelle impostazioni dell'app, proprio sotto la sezione **Nomi host**. È elencato come **Indirizzo IP esterno**

![inserimento immagine di IP SSL](./media/app-service-web-purchase-ssl-web-site/virtual-ip-address.png)

Questo indirizzo IP è diverso dall'indirizzo IP virtuale usato in precedenza per la configurazione del record A per il dominio. Se è stato impostato l'uso del metodo SSL basato su SNI o se non è stato impostato l'uso del metodo SSL, per questa voce non è elencato alcun indirizzo.

Usando gli strumenti forniti dal registrar, modificare il record A per il nome di dominio personalizzato, in modo che faccia riferimento all'indirizzo IP riportato nel passaggio precedente.

## <a name="rekey-and-sync-the-certificate"></a>Reimpostare e sincronizzare il certificato

Il certificato può essere reimpostato selezionando l'opzione **Reimposta chiavi e sincronizza** nella pagina **Proprietà certificato**.

Fare clic sul pulsante **Reimposta** per avviare il processo. Questo processo può richiedere da 1 a 10 minuti.

![inserire immagine della reimpostazione SSL](./media/app-service-web-purchase-ssl-web-site/Rekey.png)

Con la reimpostazione viene emesso un nuovo certificato da parte dell'autorità di certificazione.

<a name="notrenewed"></a>
## <a name="why-is-my-ssl-certificate-not-auto-renewed"></a>Perché il certificato SSL non viene rinnovato automaticamente?

Se il certificato SSL è configurato per il rinnovo automatico, ma non viene rinnovato automaticamente, è possibile che sia presente una verifica del dominio in sospeso. Tenere presente quanto segue: 

- GoDaddy, che genera i certificati del servizio app, richiede la verifica del dominio una volta ogni tre anni. Per la verifica del dominio, l'amministratore di dominio riceve un messaggio di posta elettronica una volta ogni tre anni. Se il messaggio di posta elettronica non viene letto oppure la verifica del dominio non viene eseguita, il certificato del servizio app non potrà essere rinnovato automaticamente. 
- Tutti i certificati del servizio app rilasciati prima del 31 marzo 2017 richiedono la nuova verifica del dominio al momento del rinnovo successivo, anche se per il certificato è abilitato il rinnovo automatico. Questo requisito deriva dalla modifica dei criteri di GoDaddy. Controllare la posta elettronica ed eseguire questa verifica del dominio occasionale per proseguire il rinnovo automatico del certificato del servizio app. 

## <a name="more-resources"></a>Altre risorse

* [Usare un certificato SSL nel codice dell'applicazione in Servizio app di Azure](app-service-web-ssl-cert-load.md)
* [Domande frequenti: Certificati del servizio app](https://blogs.msdn.microsoft.com/appserviceteam/2017/07/24/faq-app-service-certificates/)