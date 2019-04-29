---
title: Certificati necessari per il back-end nell'elenco elementi consentiti nel Gateway applicazione di Azure
description: Questo articolo fornisce esempi del modo in cui un certificato SSL può essere convertito in certificato di autenticazione e un certificato radice trusted necessari per le istanze di back-end nell'elenco elementi consentiti nel Gateway applicazione di Azure
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 3/14/2019
ms.author: absha
ms.openlocfilehash: 72ee9123ad959c0c7240d4f7a906adc1a4dd1a93
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60831731"
---
# <a name="create-certificates-for-whitelisting-backend-with-azure-application-gateway"></a>Creare certificati per back-end nell'elenco elementi consentiti con il Gateway applicazione di Azure

Per eseguire il SSL end-to-end, il gateway applicazione richiede le istanze back-end siano consentiti tramite il caricamento dei certificati radice attendibili/autenticazione. In caso di SKU v1, sono necessari i certificati di autenticazione mentre in caso di SKU v2, attendibili i certificati radice sono necessari nell'elenco elementi consentiti i certificati

In questo articolo viene spiegato come:

> [!div class="checklist"]
>
> - Esportare il certificato di autenticazione da un certificato di back-end (per SKU v1)
> - Esportare il certificato radice attendibile da un certificato di back-end (per SKU v2)

## <a name="prerequisites"></a>Prerequisiti

È necessario un certificato di back-end esistente per generare i certificati di autenticazione o i certificati radice trusted necessari per le istanze di back-end nell'elenco elementi consentiti con il gateway applicazione. Il certificato di back-end può essere uguale a certificato SSL o diverso per una maggiore sicurezza. Il gateway applicazione non fornisce alcun meccanismo per creare o acquistare un certificato SSL. A scopo di test, è possibile creare un certificato autofirmato, ma è consigliabile non usarlo per carichi di lavoro di produzione. 

## <a name="export-authentication-certificate-for-v1-sku"></a>Esportare il certificato di autenticazione (per SKU v1)

Certificato di autenticazione è necessario per le istanze di back-end dell'elenco elementi consentiti nella versione 1 di gateway applicazione lo SKU. Certificato di autenticazione è codificati nella chiave pubblica dei certificati del server back-end in Base 64 X.509 (. Formato CER). In questo esempio, si verrà Usa un certificato SSL per il certificato di back-end ed esportare la chiave pubblica da usare come certificazione l'autenticazione. Inoltre, in questo esempio, si userà lo strumento di gestione di certificati di Windows per esportare i certificati richiesti. È possibile usare qualsiasi altro strumento in base alla comodità.

Dal certificato SSL, esportare il file CER di chiave pubblica (non la chiave privata). I passaggi seguenti consentono di esportare le CER codificato di file in Base 64 X.509 (. Formato CER) per il certificato:

1. Per ottenere un file con estensione cer dal certificato, aprire **Gestire i certificati utente**. Individuare il certificato, in genere in "Certificati - utente corrente\Personale\Certificati" e fare doppio clic su. Fare clic su **Tutte le attività** e quindi su **Esporta**. Si avvia la procedura di **Esportazione guidata certificati**. Se non è possibile trovare il certificato in Utente corrente\Personale\Certificati, è possibile aver accidentalmente aperto Certificati - Computer locale" invece di "Certificati - Utente corrente". Se si vuole aprire Gestione certificati nell'ambito dell'utente corrente usando PowerShell, digitare *certmgr* nella finestra della console.

   ![Esportazione](./media/certificates-for-backend-authentication/export.png)

2. Nella procedura guidata fare clic su **Avanti**.

   ![Esportare il certificato](./media/certificates-for-backend-authentication/exportwizard.png)

3. Selezionare **No, non esportare la chiave privata** e quindi fare clic su **Avanti**.

   ![Non esportare la chiave privata](./media/certificates-for-backend-authentication/notprivatekey.png)

4. Nella pagina **Formato file di esportazione** selezionare **Codificato Base 64 X.509 (.CER)** e quindi fare clic su **Avanti**.

   ![Codifica Base 64](./media/certificates-for-backend-authentication/base64.png)

5. In **File da esportare** fare clic su **Sfoglia** e passare alla posizione in cui si vuole esportare il certificato. Per **Nome file**, assegnare un nome al file del certificato. Quindi fare clic su **Next**.

   ![Sfoglia](./media/certificates-for-backend-authentication/browse.png)

6. Fare clic su **Fine** per esportare il certificato.

   ![Finish](./media/certificates-for-backend-authentication/finish.png)

7. Il certificato è stato esportato correttamente.

   ![Success](./media/certificates-for-backend-authentication/success.png)

   Il certificato esportato è simile al seguente:

   ![Esportato](./media/certificates-for-backend-authentication/exported.png)

8. Se si apre il certificato esportato usando il Blocco note, viene visualizzato un certificato simile a questo esempio. La sezione in blu contiene le informazioni che viene caricate per il gateway applicazione. Se si apre il certificato con il Blocco note e non è simile all'esempio seguente, in genere significa che non è stato esportato usando il formato di codifica Base 64 X.509(.CER). Se si vuole usare un editor di testo diverso, è anche utile sapere che alcuni editor possono introdurre una formattazione imprevista in background. Ciò può creare problemi quando il testo viene caricato da questo certificato in Azure.

   ![Apri con il Blocco note](./media/certificates-for-backend-authentication/format.png)

## <a name="export-trusted-root-certificate-for-v2-sku"></a>Esportare il certificato radice attendibile (per SKU v2)

Attendibile il certificato radice è necessario per le istanze di back-end dell'elenco elementi consentiti nella versione 2 di gateway applicazione lo SKU. Il certificato radice è una codifica Base64 X.509 (. Certificato radice di formato CER) da certificati del server back-end. In questo esempio, si verrà Usa un certificato SSL per il certificato di back-end, esportare la chiave pubblica e quindi esportare il certificato radice dell'autorità di certificazione attendibile dalla chiave pubblica nel formato con codificata base64 per ottenere il certificato radice attendibile. 

La procedura seguente consente di esportare il file con estensione cer del certificato:

1. Usare la procedura indicata nella sezione 1 a 9 **certificato di autenticazione di esportazione da un certificato di back-end (per SKU v1)** precedente per esportare la chiave pubblica dal certificato back-end.

2. Una volta che la chiave pubblica è stata esportata, aprire il file.

   ![Certificato di autorizzazione aperta](./media/certificates-for-backend-authentication/openAuthcert.png)

   ![informazioni sul certificato](./media/certificates-for-backend-authentication/general.png)

3. Spostare il percorso di certificazione per visualizzare l'autorità di certificazione.

   ![dettagli del certificato](./media/certificates-for-backend-authentication/certdetails.png)

4. Selezionare il certificato radice e fare clic su **Visualizza certificato**.

   ![percorso certificato](./media/certificates-for-backend-authentication/rootcert.png)

   Dovrebbe essere possibile visualizzare i dettagli del certificato radice.

   ![informazioni certificato](./media/certificates-for-backend-authentication/rootcertdetails.png)

5. Spostare il **informazioni dettagliate** consente di visualizzare e fare clic su **copia su File...**

   ![certificato radice di copia](./media/certificates-for-backend-authentication/rootcertcopytofile.png)

6. A questo punto, è stato possibile estrarre i dettagli del certificato radice del certificato di back-end. Verrà visualizzato il **esportazione guidata certificati**. Ora usare i passaggi da 2 a 9 indicato nella sezione **certificato di autenticazione di esportazione da un certificato di back-end (per SKU v1)** sopra per esportare la radice attendibile in Base 64 codificato certificato X.509 (. Formato CER).

## <a name="next-steps"></a>Passaggi successivi

È ora disponibile l'autenticazione del certificato/attendibile il certificato radice in Base 64 codificata X.509 (. Formato CER). È possibile aggiungere questo al gateway applicazione all'elenco elementi consentiti i server back-end per la crittografia SSL end-to-end. Visualizzare [jak nakonfigurovat crittografia SSL end-to-end](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).