---
title: Certificati necessari per consentire i server back-end
titleSuffix: Azure Application Gateway
description: Questo articolo fornisce esempi di come un certificato TLS/SSL può essere convertito in certificato di autenticazione e certificato radice attendibile necessari per consentire le istanze back-end nel gateway applicazione di AzureThis article provides examples of how a TLS/SSL certificate can be converted to authentication certificate and trusted root certificate that are required to allow back-end instances in Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 20f588639c54b0a8b7cd304f33b5a9d633a73be6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133037"
---
# <a name="create-certificates-to-allow-the-backend-with-azure-application-gateway"></a>Creare certificati per consentire il back-end con il gateway applicazione di AzureCreate certificates to allow the backend with Azure Application Gateway

Per eseguire il TLS end-to-end, il gateway applicazione richiede che le istanze back-end siano consentite caricando certificati radice attendibili/autenticazione. Per lo SKU v1, i certificati di autenticazione sono necessari, ma per i certificati radice attendibili dello SKU v2 sono necessari per consentire i certificati.

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
>
> - Esportare il certificato di autenticazione da un certificato back-end (per lo SKU v1)Export authentication certificate from a backend certificate (for v1 SKU)
> - Esportare il certificato radice attendibile da un certificato back-end (per lo SKU v2)Export trusted root certificate from a backend certificate (for v2 SKU)

## <a name="prerequisites"></a>Prerequisiti

Un certificato back-end esistente è necessario per generare i certificati di autenticazione o i certificati radice attendibili necessari per consentire le istanze back-end con il gateway applicazione. Il certificato back-end può essere uguale al certificato TLS/SSL o diverso per una maggiore sicurezza. Il gateway applicazione non fornisce alcun meccanismo per creare o acquistare un certificato TLS/SSL. A scopo di test, è possibile creare un certificato autofirmato, ma non è consigliabile usarlo per i carichi di lavoro di produzione. 

## <a name="export-authentication-certificate-for-v1-sku"></a>Certificato di autenticazione di esportazione (per SKU v1)

È necessario un certificato di autenticazione per consentire le istanze back-end nello SKU del gateway applicazione v1. Il certificato di autenticazione è la chiave pubblica dei certificati server back-end in X.509 con codifica Base 64. CER). In questo esempio si utilizzerà un certificato TLS/SSL per il certificato back-end ed esererà la relativa chiave pubblica da utilizzare come certificazione di autenticazione. Inoltre, in questo esempio, si utilizzerà lo strumento Gestione certificati di Windows per esportare i certificati necessari. È possibile scegliere di utilizzare qualsiasi altro strumento conveniente.

Dal certificato TLS/SSL, esportare il file CER della chiave pubblica (non la chiave privata). La procedura seguente consente di esportare il file CER in formato Base 64 codificato X.509(. CER) per il certificato:

1. Per ottenere un file con estensione cer dal certificato, aprire **Gestire i certificati utente**. Individuare il certificato, in genere in 'Certificati - Utente corrente, Personale o Certificati', quindi fare clic con il pulsante destro del mouse. Fare clic su **Tutte le attività** e quindi su **Esporta**. Si avvia la procedura di **Esportazione guidata certificati**. Se non è possibile trovare il certificato in Utente corrente\Personale\Certificati, è possibile aver accidentalmente aperto Certificati - Computer locale" invece di "Certificati - Utente corrente". Se si vuole aprire Gestione certificati nell'ambito dell'utente corrente usando PowerShell, digitare *certmgr* nella finestra della console.

   ![Esportazione](./media/certificates-for-backend-authentication/export.png)

2. Nella procedura guidata, fare clic su **Avanti**.

   ![Esportare il certificato](./media/certificates-for-backend-authentication/exportwizard.png)

3. Selezionare **No, non esportare la chiave privata** e quindi fare clic su **Avanti**.

   ![Non esportare la chiave privata](./media/certificates-for-backend-authentication/notprivatekey.png)

4. Nella pagina **Formato file di esportazione** selezionare **Codificato Base 64 X.509 (.CER)** e quindi fare clic su **Avanti**.

   ![Codifica Base 64](./media/certificates-for-backend-authentication/base64.png)

5. In **File da esportare** fare clic su **Sfoglia** e passare alla posizione in cui si vuole esportare il certificato. Per **Nome file**, assegnare un nome al file del certificato. Fare quindi clic su **Avanti**.

   ![Sfoglia](./media/certificates-for-backend-authentication/browse.png)

6. Fare clic su **Fine** per esportare il certificato.

   ![Finish](./media/certificates-for-backend-authentication/finish.png)

7. Il certificato è stato esportato correttamente.

   ![Operazione completata](./media/certificates-for-backend-authentication/success.png)

   Il certificato esportato è simile al seguente:

   ![Esportato](./media/certificates-for-backend-authentication/exported.png)

8. Se si apre il certificato esportato usando il Blocco note, viene visualizzato un certificato simile a questo esempio. La sezione in blu contiene le informazioni caricate nel gateway applicazione. Se si apre il certificato con Blocco note e non è simile al seguente, in genere questo significa che non è stato esportato utilizzando il codificato Base 64 X.509(. CER). Se si vuole usare un editor di testo diverso, è anche utile sapere che alcuni editor possono introdurre una formattazione imprevista in background. Ciò può creare problemi quando il testo viene caricato da questo certificato in Azure.

   ![Apri con il Blocco note](./media/certificates-for-backend-authentication/format.png)

## <a name="export-trusted-root-certificate-for-v2-sku"></a>Esportare il certificato radice attendibile (per lo SKU v2)Export trusted root certificate (for v2 SKU)

Il certificato radice attendibile è necessario per autorizzare le istanze back-end nello SKU del gateway applicazione v2. Il certificato radice è un X.509 con codifica Base 64(. CER) formatta il certificato radice dai certificati del server back-end. In questo esempio verrà utilizzato un certificato TLS/SSL per il certificato back-end, verrà esportato la chiave pubblica e quindi il certificato radice della CA attendibile verrà esportata dalla chiave pubblica in formato codificato base64 per ottenere il certificato radice attendibile. I certificati intermedi devono essere inclusi nel certificato server e installati nel server back-end.

I passaggi seguenti consentono di esportare il file CER per il certificato:

1. Usare i passaggi da 1 a 9 indicati nella sezione Esportare il certificato di **autenticazione da un certificato back-end (per lo SKU v1)** precedente per esportare la chiave pubblica dal certificato back-end.

2. Una volta che la chiave pubblica è stata esportata, aprire il file.

   ![Certificato di autorizzazione aperto](./media/certificates-for-backend-authentication/openAuthcert.png)

   ![informazioni sul certificato](./media/certificates-for-backend-authentication/general.png)

3. Passare alla visualizzazione Percorso certificazione per visualizzare l'autorità di certificazione.

   ![dettagli del certificato](./media/certificates-for-backend-authentication/certdetails.png)

4. Selezionare il certificato radice e fare clic su **Visualizza certificato**.

   ![percorso del certificato](./media/certificates-for-backend-authentication/rootcert.png)

   Dovrebbero essere visualizzati i dettagli del certificato radice.

   ![informazioni sul certificato](./media/certificates-for-backend-authentication/rootcertdetails.png)

5. Passare alla vista **Dettagli** e fare clic su **Copia su file...**

   ![copia certificato radice](./media/certificates-for-backend-authentication/rootcertcopytofile.png)

6. A questo punto, sono stati estratti i dettagli del certificato radice dal certificato back-end. Verrà visualizzata **l'Esportazione guidata certificati**. A questo punto usare i passaggi 2-9 indicati nella sezione Esportare il certificato di **autenticazione da un certificato back-end (per lo SKU v1)** precedente per esportare il certificato radice attendibile nel X.509 con codifica Base 64. CER).

## <a name="next-steps"></a>Passaggi successivi

A questo punto si dispone del certificato di autenticazione/certificato radice attendibile in X.509 con codifica Base 64. CER). È possibile aggiungerlo al gateway applicazione per elencare i server back-end per la crittografia TLS end-to-end. Vedere [Configurare TLS end-to-end tramite il gateway applicazione con PowerShell](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).

