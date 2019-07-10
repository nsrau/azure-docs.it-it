---
title: Esercitazione per ordinare dischi di Azure Data Box | Microsoft Docs
description: Usare questa esercitazione per scoprire come iscriversi e ordinare dischi di Azure Data Box per importare dati in Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 39d39b313cd2e65989989080d4cb0729b0be1e65
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561458"
---
# <a name="tutorial-order-an-azure-data-box-disk"></a>Esercitazione: Ordinare un disco di Azure Data Box

Azure Data Box Disk è una soluzione cloud ibrida che consente di importare i dati in locale in Azure in modo rapido, semplice e affidabile. È possibile trasferire i dati su dischi SSD (Solid State Disk) forniti da Microsoft e rispedire i dischi. Questi dati vengono poi caricati in Azure.

Questa esercitazione descrive come è possibile ordinare un disco di Azure Data Box. Questa esercitazione descrive quanto segue:

> [!div class="checklist"]
> * Ordinare un disco di Data Box
> * Monitorare l'ordine
> * Annullare l'ordine

## <a name="prerequisites"></a>Prerequisiti

Prima della distribuzione completare i prerequisiti di configurazione seguenti per il servizio Data Box e Data Box Disk.

### <a name="for-service"></a>Per il servizio

Prima di iniziare, verificare che:
- Si dispone dell'account di archiviazione di Microsoft Azure con credenziali di accesso.
- La sottoscrizione usata per il servizio Data Box sia di uno dei tipi seguenti:
    - Contratto Enterprise Microsoft. Altre informazioni sui [contratti Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/).
    - Cloud Solution Provider (CSP). Altre informazioni sul [programma Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview).
- Si abbia accesso alla sottoscrizione come proprietario o collaboratore per creare un ordine Data Box.

### <a name="for-device"></a>Per il dispositivo

Prima di iniziare, verificare che:
- Sia disponibile un computer client da cui è possibile copiare i dati. Il computer client deve:
    - Eseguire un [sistema operativo supportato](data-box-disk-system-requirements.md#supported-operating-systems-for-clients).
    - Avere altro [software richiesto](data-box-disk-system-requirements.md#other-required-software-for-windows-clients) installato se è un client Windows.  

## <a name="order-data-box-disk"></a>Ordinare un disco di Data Box

Accedere a:

- Portale di Azure all'URL https://portal.azure.com per ordinare Data Box Disk.
- Portale di Azure per enti pubblici all'URL https://portal.azure.us. Per altri dettagli, vedere l'articolo su come [connettersi ad Azure per enti pubblici con il portale](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-portal).

Per ordinare Data Box Disk, seguire questa procedura.

1. Nell'angolo superiore sinistro del portale fare clic su **+ Crea una risorsa** e cercare *Azure Data Box*. Fare clic su **Azure Data Box**.
    
   ![Cercare Azure Data Box 1](media/data-box-disk-deploy-ordered/search-data-box11.png)

2. Fare clic su **Create**(Crea).

3. Controllare se il servizio Data Box è disponibile nella propria area. Immettere o selezionare le informazioni seguenti e quindi fare clic su **Applica**.

    ![Selezionare l'opzione Data Box Disk](media/data-box-disk-deploy-ordered/select-data-box-sku-1.png)

    |Impostazione|Valore|
    |---|---|
    |Sottoscrizione|Selezionare la sottoscrizione per cui è abilitato il servizio Data Box.<br> La sottoscrizione viene collegata all'account di fatturazione. |
    |Tipo di trasferimento| Importa in Azure|
    |Paese di origine | Selezionare il paese/area in cui si trovano attualmente i dati.|
    |Area di Azure di destinazione|Selezionare l'area di Azure in cui si vogliono trasferire i dati.|

  
5.  Selezionare **Data Box Disk**. La capacità massima della soluzione per un singolo ordine di 5 dischi è di 35 TB. È possibile creare più ordini per volumi di dati maggiori.

     ![Selezionare l'opzione Data Box Disk](media/data-box-disk-deploy-ordered/select-data-box-sku-zoom.png)

6.  In **Ordine** specificare i **Dettagli ordine**. Immettere o selezionare le informazioni seguenti.

    |Impostazione|Valore|
    |---|---|
    |NOME|Specificare un nome descrittivo per tenere traccia dell'ordine.<br> Il nome può contenere da 3 a 24 caratteri che possono essere lettere, numeri e trattini. <br> Il nome deve iniziare e terminare con una lettera o un numero. |
    |Gruppo di risorse| Usare un gruppo esistente o crearne uno nuovo. <br> Un gruppo di risorse è un contenitore logico per le risorse che possono essere gestite o distribuite insieme. |
    |Area di Azure di destinazione| Selezionare l'area per l'account di archiviazione.<br> Attualmente sono supportati gli account di archiviazione in tutte le aree di Stati Uniti, Europa occidentale e settentrionale, Canada e Australia. |
    |Dimensioni dei dati stimate in TB| Immettere una stima in TB. <br>In base alle dimensioni dei dati, Microsoft invia un numero appropriato di dischi SSD da 8 TB (con capacità utilizzabile di 7 TB). <br>La capacità massima utilizzabile nei 5 dischi è di 35 TB. |
    |Passkey disco| Se si seleziona **Usare una chiave personalizzata anziché la passkey generata da Azure**, specificare la passkey del disco. <br> Immettere una chiave alfanumerica di lunghezza compresa tra 12 e 32 caratteri e che contenga almeno un carattere numerico e un carattere speciale. I caratteri speciali consentiti sono `@?_+`. <br> Se si preferisce, è possibile ignorare questa opzione e usare la passkey generata da Azure per sbloccare i dischi.|
    |Destinazione di archiviazione     | Scegliere tra account di archiviazione e/o dischi gestiti. <br> In base all'area di Azure specificata, selezionare un account di archiviazione dall'elenco filtrato di un account di archiviazione esistente. Il Data Box Disk può essere collegato solamente a 1 account di archiviazione. <br> È anche possibile creare un nuovo account **Utilizzo generico v1**, **Utilizzo generico v2** o un **account di archiviazione BLOB**. <br>Sono supportati gli account di archiviazione con reti virtuali. Per consentire al servizio Data Box di lavorare con gli account di archiviazione protetti, abilitare i servizi attendibili all'interno delle impostazioni del firewall di rete dell'account di archiviazione. Per altre informazioni, vedere come [Aggiungere Azure Data Box come servizio attendibile](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).|

    Se si usa l'account di archiviazione come destinazione, vedere lo screenshot seguente:

    ![Ordine di Data Box Disk per l'account di archiviazione](media/data-box-disk-deploy-ordered/order-storage-account.png)

    Se si usa Data Box Disk per creare dischi gestiti da dischi rigidi virtuali in locale, è necessario fornire anche le informazioni seguenti:

    |Impostazione  |Valore  |
    |---------|---------|
    |Gruppo di risorse     | Se si prevede la creazione di dischi gestiti da dischi rigidi virtuali in locale, creare un nuovo gruppo di risorse. Usare un gruppo di risorse esistente solo se è stato creato per l'ordine di Data Box Disk per il disco gestito dal servizio Data Box. <br> È supportato un solo gruppo di risorse.|

    ![Ordine di Data Box Disk per disco gestito](media/data-box-disk-deploy-ordered/order-managed-disks.png)

    L'account di archiviazione specificato per i dischi gestiti viene usato come account di archiviazione di staging. Il servizio Data Box carica i dischi rigidi virtuali nell'account di archiviazione di staging e poi li converte in dischi gestiti e li sposta nei gruppi di risorse. Per altre informazioni, vedere [Verificare il caricamento dei dati in Azure](data-box-disk-deploy-picked-up.md#verify-data-upload-to-azure).

13. Fare clic su **Avanti**.

    ![Specificare i dettagli dell'ordine](media/data-box-disk-deploy-ordered/data-box-order-details.png)

14. Nella scheda **Indirizzo di spedizione** specificare nome e cognome, nome e indirizzo postale della società e un numero di telefono valido. Fare clic su **Convalida indirizzo**. Il servizio convalida l'indirizzo di spedizione per la disponibilità del servizio. Se il servizio è disponibile per l'indirizzo di spedizione specificato, si riceve una notifica in tal senso. 

    ![Specificare l'indirizzo di spedizione](media/data-box-disk-deploy-ordered/data-box-shipping-address.png)
15. In **Dettagli notifica** specificare gli indirizzi di posta elettronica. Il servizio invia notifiche tramite posta elettronica per qualsiasi aggiornamento dello stato dell'ordine agli indirizzi di posta elettronica specificati. 

    È consigliabile usare un indirizzo di posta elettronica di gruppo in modo da continuare a ricevere le notifiche anche se un amministratore del gruppo non è disponibile.

16. Verificare il **riepilogo** delle informazioni relative a ordine, contatti, notifiche e informativa sulla privacy. Selezionare la casella corrispondente per accettare le condizioni per la privacy.

17. Fare clic su **Ordina**. Per la creazione dell'ordine sono richiesti pochi minuti.

 
## <a name="track-the-order"></a>Monitorare l'ordine

Dopo aver inserito l'ordine, è possibile monitorare lo stato dell'ordine dal portale di Azure. Passare all'ordine e quindi a **Panoramica** per visualizzare lo stato. Il portale mostra il processo con stato **Ordinato**.

![Stato del disco di Data Box ordinato](media/data-box-disk-deploy-ordered/data-box-portal-ordered.png) 

Se i dischi non sono disponibili, si riceverà una notifica. Se i dischi sono disponibili, Microsoft identifica i dischi per la spedizione e prepara il pacchetto dei dischi. Durante la preparazione dei dischi vengono eseguite le azioni seguenti:

- I dischi vengono crittografati con crittografia BitLocker AES-128.  
- I dischi vengono bloccati per impedire l'accesso non autorizzato.
- Durante questo processo viene generata la passkey che sblocca i dischi.

Una volta completata la preparazione dei dischi, lo stato dell'ordine nel portale diventa **Elaborato**.

Microsoft prepara e spedisce quindi i dischi tramite un vettore dell'area. Verrà comunicato un numero di tracciabilità dopo la spedizione dei dischi. Il portale mostra l'ordine con lo stato **Spedito**.

## <a name="cancel-the-order"></a>Annullare l'ordine

Per annullare l'ordine, nel portale di Azure passare a **Panoramica** e fare clic su **Annulla** sulla barra dei comandi.

È possibile annullare solo quando i dischi sono stati ordinati ed è in corso l'elaborazione dell'ordine per la spedizione. Dopo l'elaborazione dell'ordine, non è più possibile annullarlo.

![Annullare l'ordine](media/data-box-disk-deploy-ordered/cancel-order1.png)

Per eliminare un ordine annullato, passare a **Panoramica** e fare clic su **Elimina** dalla barra dei comandi.


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati presentati argomenti relativi ad Azure Data Box, ad esempio:

> [!div class="checklist"]
> * Ordinare un disco di Data Box
> * Monitorare l'ordine
> * Annullare l'ordine

Passare all'esercitazione successiva per informazioni su come configurare Data Box Disk.

> [!div class="nextstepaction"]
> [Configurare Azure Data Box Disk](./data-box-disk-deploy-set-up.md)
