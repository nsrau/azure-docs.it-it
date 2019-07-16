---
title: Esercitazione per ordinare Azure Data Box Heavy | Microsoft Docs
description: Informazioni sui prerequisiti di distribuzione e su come ordinare un dispositivo Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: c7fbd37ff8d40f27e0ca18a6f9816d3d96422ab9
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592403"
---
# <a name="tutorial-order-azure-data-box-heavy"></a>Esercitazione: Ordinare Azure Data Box Heavy


Azure Data Box Heavy è una soluzione ibrida che consente di importare i dati locali in Azure in modo rapido, semplice e affidabile. I dati devono essere trasferiti in un dispositivo di archiviazione da 770 TB (capacità utilizzabile approssimativa) fornito da Microsoft e quindi il dispositivo deve essere rispedito a Microsoft. Questi dati vengono poi caricati in Azure.

Questa esercitazione descrive come è possibile ordinare un dispositivo Azure Data Box Heavy. Questa esercitazione descrive quanto segue:

> [!div class="checklist"]
> * Prerequisiti per Data Box Heavy
> * Ordinare un dispositivo Data Box Heavy
> * Monitorare l'ordine
> * Annullare l'ordine

## <a name="prerequisites"></a>Prerequisiti

Prima di distribuire il dispositivo, completare i prerequisiti di configurazione seguenti per il servizio e il dispositivo Data Box.

### <a name="for-installation-site"></a>Per il sito di installazione

Prima di iniziare, verificare che:

- Le dimensioni del dispositivo siano adeguate a porte e ingressi standard. Assicurarsi tuttavia che le dimensioni del dispositivo siano adeguate a tutti gli ingressi interessati. Le misure del dispositivo sono: larghezza: 66,04 cm, lunghezza: 121,92 cm, altezza: 71,12 cm.
- Sia disponibile un ascensore o una rampa, se si prevede di installare il dispositivo in un piano diverso dal piano terra. Il dispositivo pesa circa 227 kg.
- Nel data center sia presente una superficie piana, vicina a una connessione di rete disponibile, idonea a un dispositivo con questo ingombro.


### <a name="for-service"></a>Per il servizio

Prima di iniziare, verificare che:
- Si dispone dell'account di archiviazione di Microsoft Azure con credenziali di accesso.
- La sottoscrizione usata per il servizio Data Box sia di uno dei tipi seguenti:
    - Contratto Enterprise Microsoft. Altre informazioni sui [contratti Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/).
    - Cloud Solution Provider (CSP). Altre informazioni sul [programma Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview).
    - Microsoft Azure Sponsorship Altre informazioni sul [programma Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/).

- Si abbia accesso alla sottoscrizione come proprietario o collaboratore per creare un ordine di Data Box Heavy.

### <a name="for-device"></a>Per il dispositivo

Prima di iniziare, verificare che:
- Il dispositivo sia disimballato.
- Sia disponibile un computer host connesso alla rete del data center. Data Box Heavy copierà i dati da questo computer. Il computer host deve eseguire un sistema operativo supportato come descritto nei [requisiti di sistema per Azure Data Box Heavy](data-box-system-requirements.md).
- Sia disponibile un computer portatile con un cavo RJ-45 per connettersi all'interfaccia utente locale e configurare il dispositivo. Usare il portatile per configurare una sola volta ogni nodo del dispositivo.
- Il data center disponga di una rete ad alta velocità. È consigliabile disporre di una connessione di almeno 10 GbE.
- Sia disponibile un cavo da 40 Gbps o da 10 Gbps per ogni nodo del dispositivo. Scegliere cavi compatibili con l'interfaccia di rete [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html):

    - Per il cavo da 40 Gbps, l'estremità da collegare al dispositivo deve essere di tipo QSFP+.
    - Il cavo da 10 Gbps deve avere un connettore SFP+ sul lato da collegare a uno switch 10-G e un adattatore da QSFP+ a SFP+ (o l'adattatore QSA) sull'estremità da collegare al dispositivo.
    - I cavi di alimentazione sono inclusi con il dispositivo.

## <a name="order-data-box-heavy"></a>Ordinare Data Box Heavy

Seguire questa procedura nel portale di Azure per ordinare un dispositivo.

1. Usare le credenziali di Microsoft Azure per accedere all'URL [https://portal.azure.com](https://portal.azure.com).
2. Selezionare **+ Crea una risorsa** e cercare *Azure Data Box*. Selezionare **Azure Data Box**.
    
   [![Cercare Azure Data Box 1](media/data-box-deploy-ordered/search-azure-data-box1.png)](media/data-box-deploy-ordered/search-azure-data-box1.png#lightbox)

3. Selezionare **Create** (Crea).

4. Controllare se il servizio Data Box è disponibile nella propria area. Immettere o selezionare le informazioni seguenti e quindi selezionare **Applica**.

    |Impostazione  |Valore  |
    |---------|---------|
    |Sottoscrizione     | Selezionare una sottoscrizione di tipo Contratto Enterprise, CSP o Azure Sponsorship per il servizio Data Box. <br> La sottoscrizione viene collegata all'account di fatturazione.       |
    |Tipo di trasferimento     | Selezionare **Importa in Azure**.        |
    |Paese di origine     | Selezionare il paese/area in cui si trovano attualmente i dati.         |
    |Area di Azure di destinazione     | Selezionare l'area di Azure in cui si vogliono trasferire i dati.        |

    [![Selezionare la disponibilità della famiglia di prodotti Data Box](media/data-box-deploy-ordered/select-data-box-option1.png)](media/data-box-deploy-ordered/select-data-box-option1.png#lightbox)

5. Selezionare **Data Box Heavy**. La capacità massima che è possibile usare per un singolo ordine è di 770 TB.

    [![Selezionare Data Box Heavy](media/data-box-heavy-deploy-ordered/select-data-box-heavy.png)

6. In **Ordine** specificare i **Dettagli ordine**. Immettere o selezionare le informazioni seguenti e quindi selezionare **Avanti**.
    
    |Impostazione  |Valore  |
    |---------|---------|
    |NOME     | Specificare un nome descrittivo per tenere traccia dell'ordine. <br> Il nome può contenere da 3 a 24 caratteri che possono essere lettere, numeri e trattini. <br> Il nome deve iniziare e terminare con una lettera o un numero.      |
    |Gruppo di risorse     | Usare un gruppo esistente o crearne uno nuovo. <br> Un gruppo di risorse è un contenitore logico per le risorse che possono essere gestite o distribuite insieme.         |
    |Area di Azure di destinazione     | Selezionare l'area per l'account di archiviazione. <br> Per altre informazioni, vedere [Disponibilità a livello di area](https://azure.microsoft.com/global-infrastructure/services/?products=databox).        |
    |Destinazione di archiviazione     | Scegliere tra account di archiviazione e/o dischi gestiti. <br> In base all'area di Azure specificata, selezionare uno o più account di archiviazione nell'elenco filtrato di un account di archiviazione esistente. <br>Data Box Heavy può essere collegato a un massimo di 10 account di archiviazione. <br> È anche possibile creare un nuovo account **Utilizzo generico v1**, **Utilizzo generico v2** o un **account di archiviazione BLOB**. <br> Gli account di Azure Data Lake Storage Gen 2 non sono supportati. Vedere le [gli account di archiviazione supportati con il dispositivo](data-box-heavy-system-requirements.md#supported-storage-accounts). <br>Sono supportati gli account di archiviazione con reti virtuali. Per consentire al servizio Data Box di lavorare con gli account di archiviazione protetti, abilitare i servizi attendibili all'interno delle impostazioni del firewall di rete dell'account di archiviazione. Per altre informazioni, vedere come [Aggiungere il servizio Azure Data Box come servizio attendibile](../storage/common/storage-network-security.md#exceptions).|

    Se si usa l'account di archiviazione come destinazione, vedere lo screenshot seguente:

    ![Ordine di Data Box Heavy per l'account di archiviazione](media/data-box-heavy-deploy-ordered/order-storage-account.png)

    Se oltre all'account di archiviazione come destinazione di archiviazione si usa anche Data Box Heavy per creare dischi gestiti da dischi rigidi virtuali locali, è necessario fornire le informazioni seguenti:

    |Impostazione  |Valore  |
    |---------|---------|
    |Gruppi di risorse     | Se si prevede la creazione di dischi gestiti da dischi rigidi virtuali in locale, creare nuovi gruppi di risorse. È possibile usare un gruppo di risorse solo se questo è stato creato in precedenza durante la creazione di un ordine di Data Box Heavy per il disco gestito dal servizio Data Box. <br> Specificare più gruppi di risorse separati da punti e virgola. Vengono supportati un massimo di 10 gruppi di risorse.|

    ![Ordine di Data Box Heavy per disco gestito](media/data-box-heavy-deploy-ordered/order-managed-disks.png)

    L'account di archiviazione specificato per i dischi gestiti viene usato come account di archiviazione di staging. Il servizio Data Box carica i dischi rigidi virtuali come BLOB di pagine per l'account di archiviazione di staging prima di convertirli in dischi gestiti e spostarli nei gruppi di risorse. Per altre informazioni, vedere [Verificare il caricamento dei dati in Azure](data-box-deploy-picked-up.md#verify-data-upload-to-azure).

7. In **Indirizzo di spedizione** specificare nome e cognome, nome e indirizzo postale della società e un numero di telefono valido. Selezionare **Convalida indirizzo**. 

    Il servizio convalida l'indirizzo di spedizione per la disponibilità del servizio. Se il servizio è disponibile per l'indirizzo di spedizione specificato, si riceve una notifica in tal senso. Selezionare **Avanti**.

8. In **Dettagli notifica** specificare gli indirizzi di posta elettronica. Il servizio invia notifiche tramite posta elettronica per qualsiasi aggiornamento dello stato dell'ordine agli indirizzi di posta elettronica specificati.

    È consigliabile usare un indirizzo di posta elettronica di gruppo in modo da continuare a ricevere le notifiche anche se un amministratore del gruppo non è disponibile.

9. Verificare il **riepilogo** delle informazioni relative a ordine, contatti, notifiche e informativa sulla privacy. Selezionare la casella corrispondente per accettare le condizioni per la privacy.

10. Selezionare **Ordina**. Per la creazione dell'ordine sono richiesti pochi minuti.


## <a name="track-the-order"></a>Monitorare l'ordine

Dopo aver inserito l'ordine, è possibile monitorare lo stato dell'ordine dal portale di Azure. Passare all'ordine di Data Box Heavy e quindi a **Panoramica** per visualizzare lo stato. Il portale mostra l'ordine con stato **Ordinato**.

Se il dispositivo non è disponibile, si riceverà una notifica. Se il dispositivo è disponibile, Microsoft identifica il dispositivo per la spedizione e lo prepara. Durante la preparazione del dispositivo vengono eseguite le azioni seguenti:

- Vengono create condivisioni SMB per ogni account di archiviazione associato al dispositivo.
- Per ogni condivisione vengono generate le credenziali di accesso, come nome utente e password.
- Viene generata anche una password per il dispositivo che consente di sbloccare il dispositivo.
- Data Box Heavy viene bloccato per impedire qualsiasi accesso non autorizzato al dispositivo.

Una volta completata la preparazione del dispositivo, lo stato dell'ordine nel portale diventa **Elaborato**.

![Ordine di Data Box Heavy elaborato](media/data-box-overview/data-box-order-status-processed.png)

Microsoft prepara e spedisce quindi il dispositivo tramite un corriere locale. Si riceverà un numero di tracciabilità dopo la spedizione del dispositivo. Il portale mostra l'ordine con lo stato **Spedito**.

![Ordine di Data Box Heavy spedito](media/data-box-overview/data-box-order-status-dispatched.png)

## <a name="cancel-the-order"></a>Annullare l'ordine

Per annullare l'ordine, nel portale di Azure passare a **Panoramica** e fare clic su **Annulla** sulla barra dei comandi.

Dopo aver effettuato un ordine, è possibile annullarlo in qualsiasi momento prima che il suo stato venga contrassegnato come elaborato.
 
Per eliminare un ordine annullato, passare a **Panoramica** e fare clic su **Elimina** dalla barra dei comandi.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati presentati argomenti relativi ad Azure Data Box Heavy, ad esempio:

> [!div class="checklist"]
> * Prerequisiti
> * Ordinare Data Box Heavy
> * Monitorare l'ordine
> * Annullare l'ordine

Passare all'esercitazione successiva per informazioni su come configurare Data Box Heavy.

> [!div class="nextstepaction"]
> [Configurare Azure Data Box Heavy](./data-box-heavy-deploy-set-up.md)
