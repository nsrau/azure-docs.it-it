---
title: Avvio rapido di Microsoft Azure Data Box Heavy | Microsoft Docs
description: Informazioni su come distribuire rapidamente Azure Data Box Heavy nel portale di Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: quickstart
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: b2ebb4c5a0df360cede084a90ebe5da041a08aa0
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/05/2019
ms.locfileid: "70241385"
---
::: zone target = "docs"

# <a name="quickstart-deploy-azure-data-box-heavy-using-the-azure-portal"></a>Guida introduttiva: Distribuire Azure Data Box Heavy usando il portale di Azure

Questo argomento di avvio rapido descrive come distribuire Azure Data Box Heavy tramite il portale di Azure. I passaggi includono come cablare, configurare e copiare i dati in Data Box Heavy per il caricamento in Azure. La guida introduttiva viene eseguita nel portale di Azure e nell'interfaccia utente Web locale del dispositivo.

Per istruzioni dettagliate sulla distribuzione e sul monitoraggio, vedere [Esercitazione: Ordinare Azure Data Box Heavy](data-box-heavy-deploy-ordered.md)

## <a name="prerequisites"></a>Prerequisiti

Prima di distribuire il dispositivo, completare i prerequisiti di configurazione seguenti per il sito di installazione, il servizio e il dispositivo Data Box.

### <a name="for-installation-site"></a>Per il sito di installazione

Prima di iniziare, verificare che:

- Le dimensioni del dispositivo siano adeguate alle vie di accesso ai locali. Le misure del dispositivo sono: larghezza: 66,04 cm, lunghezza: 121,92 cm, altezza: 71,12 cm.
- Sia disponibile un ascensore o una rampa, se si prevede di installare il dispositivo in un piano diverso dal piano terra.
- Siano disponibili due persone per maneggiare il dispositivo, che pesa circa 227 kg. ed è montato su ruote.
- Nel data center sia disponibile una superficie piana, vicina a una connessione di rete disponibile, idonea a un dispositivo con questo ingombro.

### <a name="for-service"></a>Per il servizio

Prima di iniziare, verificare che:

- Si dispone dell'account di archiviazione di Microsoft Azure con credenziali di accesso.
- La sottoscrizione usata per il servizio Data Box sia di tipo [Microsoft Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), [Cloud Solution Provider (CSP)](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview) o [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/).
- Si abbia accesso alla sottoscrizione come proprietario o collaboratore per creare un ordine Data Box Heavy.

### <a name="for-device"></a>Per il dispositivo

Prima di iniziare, verificare che:

- Siano state esaminate le [linee guida per la sicurezza del Data Box Heavy](data-box-safety.md).
- Sia disponibile un computer host connesso alla rete del data center. Il servizio Data Box Heavy copierà i dati da questo computer. Il computer host deve eseguire un [sistema operativo supportato](data-box-heavy-system-requirements.md).
- Sia disponibile un computer portatile con un cavo RJ-45 per connettersi all'interfaccia utente locale e configurare il dispositivo. Usare il portatile per configurare una sola volta ogni nodo del dispositivo.
- Il data center disponga di una rete ad alta velocità con una connessione da almeno 10 GbE.
- È necessario un cavo da 40 Gbps o da 10 Gbps per ogni nodo del dispositivo. Scegliere cavi compatibili con l'interfaccia di rete Mellanox MCX314A-BCCT:
    - Per il cavo da 40 Gbps, l'estremità da collegare al dispositivo deve essere di tipo QSFP+.
    - Il cavo da 10 Gbps deve avere un connettore SFP+ sul lato da collegare a uno switch 10-G e un adattatore da QSFP+ a SFP+ (o l'adattatore QSA) sull'estremità da collegare al dispositivo.
- I cavi di alimentazione si trovano in una vano nella parte posteriore del dispositivo.


## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="order"></a>Ordine

Questo passaggio richiede circa 5 minuti.

1. Creare una nuova risorsa Azure Data Box nel portale di Azure.
2. Selezionare una sottoscrizione esistente abilitata per questo servizio e scegliere **Importa** come tipo di trasferimento. Specificare il **paese di origine** in cui risiedono i dati e l'**area di destinazione di Azure** per il trasferimento dei dati.
3. Selezionare **Data Box Heavy**. La capacità massima utilizzabile è di 770 TB ed è possibile creare più ordini per dimensioni dei dati più grandi.
4. Immettere i dettagli dell'ordine e le informazioni per la spedizione. Se il servizio è disponibile nella propria area, specificare gli indirizzi di posta elettronica per le notifiche, controllare il riepilogo e quindi creare l'ordine.

Dopo aver creato l'ordine, il dispositivo verrà preparato per la spedizione.

::: zone-end

::: zone target = "chromeless"

# <a name="cable-and-connect-to-your-device"></a>Cablare e connettersi al dispositivo

Dopo aver esaminato i prerequisiti, cablare e connettersi al dispositivo.

::: zone-end

## <a name="cable-for-power"></a>Cablare l'alimentazione

Questo passaggio richiede circa 5 minuti.

Quando si riceve il Data Box Heavy, procedere come segue per collegare il dispositivo all'alimentazione e accenderlo.

1. Qualora risulti che il dispositivo sia stato manomesso o danneggiato, non procedere. Contattare il supporto tecnico Microsoft per richiedere un dispositivo sostitutivo.
2. Spostare il dispositivo nel sito di installazione e bloccare le ruote posteriori.
3. Connettere tutti e quattro i cavi di alimentazione agli alimentatori sul retro del dispositivo.
4. Usare i pulsanti di alimentazione nel pannello anteriore per attivare i nodi del dispositivo.

## <a name="cable-first-node-for-network"></a>Cablare il primo nodo per la rete

Questa operazione richiede circa 10-15 minuti.

1. Usare il cavo di rete RJ-45 CAT 6 per connettere il computer host alla porta di gestione sul dispositivo.
2. Usare il cavo di rame QSFP+ Twinax per connettere almeno una interfaccia di rete a 40 Gbps (preferita rispetto a quella a 1 Gbps), DATA 1 o DATA 2 per i dati. Se si usa uno switch a 10 Gbps, usare un cavo in rame Twinax SFP+ con un adattatore da QSFP+ a SFP+ (l'adattatore QSA) per connettere l'interfaccia di rete a 40 Gbps per i dati.
3. Cablare il dispositivo come illustrato di seguito.  

    ![Data Box Heavy cablato](media/data-box-heavy-quickstart-portal/data-box-heavy-ports-cabled.png)  

## <a name="configure-first-node"></a>Configurare il primo nodo

Questa operazione richiede circa 5-7 minuti.

1. Per ottenere la password del dispositivo, passare a **Generale > Dettagli dispositivo** nel [portale di Azure](https://portal.azure.com). Per entrambi i nodi del dispositivo si usa la stessa password.
2. Assegnare un indirizzo IP statico 192.168.100.5 e la subnet 255.255.255.0 alla scheda Ethernet nel computer usato per connettersi a Data Box Heavy. Accedere all'interfaccia utente Web locale del dispositivo all'indirizzo `https://192.168.100.10`. La connessione potrebbe richiedere fino a 5 minuti dopo l'accensione del dispositivo.
3. Eseguire l'accesso usando la password dal portale di Azure. Viene visualizzato un errore in cui si indica che si è verificato un problema con il certificato di sicurezza del sito Web. Seguire le istruzioni specifiche del browser per passare alla pagina Web.
4. Per impostazione predefinita, le impostazioni di rete per le interfacce, ad esclusione di MGMT, sono configurate come DHCP. Se necessario, è possibile configurare queste interfacce come statiche e specificare un indirizzo IP.

## <a name="cable-and-configure-the-second-node"></a>Cablare e configurare il secondo nodo

Questa operazione richiede circa 15-20 minuti.

Seguire la procedura indicata per il primo nodo per cablare e configurare il secondo nodo nel dispositivo.  


::: zone target = "docs"

## <a name="copy-data"></a>Copiare i dati

Il tempo per completare questa operazione dipende dalla dimensione dei dati e dalla velocità della rete in cui vengono copiati.
 
1. Copiare dati in entrambi i nodi del dispositivo usando entrambe le interfacce dati a 40 Gbps in parallelo.

    - Se si usa un host di Windows, usare uno strumento di copia file compatibile con SMB, ad esempio [Robocopy](https://technet.microsoft.com/library/ee851678.aspx).
    - Per l'host NFS, usare il comando `cp` oppure `rsync` per copiare i dati.
2. Connettersi alle condivisioni nel dispositivo usando il percorso:`\\<IP address of your device>\ShareName`. Per ottenere le credenziali di accesso alle condivisioni, passare alla pagina **Connetti e copia** nell'interfaccia utente Web locale di Data Box Heavy.
3. Accertarsi che i nomi delle condivisioni e delle cartelle e i dati seguano le linee guida descritte nei [limiti dei servizi Archiviazione di Azure e Data Box Heavy](data-box-heavy-limits.md).

## <a name="prepare-to-ship"></a>Preparare per la spedizione

Il tempo per completare questa operazione dipende dalla dimensione dei dati.

1. Dopo che la copia dei dati è stata completata senza errori, passare alla pagina **Prepara per la spedizione** nell'interfaccia utente Web locale e avviare la preparazione della spedizione.
2. Una volta completata correttamente la **preparazione per la spedizione** su entrambi i nodi, disattivare il dispositivo dall'interfaccia utente Web locale.

## <a name="ship-to-azure"></a>Spedire ad Azure

Questa operazione richiede circa 15-20 minuti.

1. Rimuovere i cavi e riporli nel vano nella parte posteriore del dispositivo.
2. Pianificare un ritiro con il corriere di zona.
3. Contattare il team [Data Box Operations](mailto:DataBoxOps@microsoft.com) per informarli sul ritiro e ottenere l'etichetta per la spedizione di ritorno.
4. L'etichetta per la spedizione di ritorno deve essere visibile sul pannello frontale trasparente del dispositivo.

## <a name="verify-data"></a>Verificare i dati

Il tempo per completare questa operazione dipende dalla dimensione dei dati.

1. Quando il dispositivo Data Box Heavy viene connesso alla rete del data center di Azure, i dati vengono caricati automaticamente in Azure.
2. Il servizio Data Box invia notifica del completamento della copia dei dati tramite il portale di Azure.

    1. Controllare i log degli errori per rilevare eventuali errori e intraprendere le azioni appropriate.
    2. Verificare la presenza dei dati negli account di archiviazione prima di eliminarli dall'origine.

## <a name="clean-up-resources"></a>Pulire le risorse

Il completamento di questo passaggio richiede 2-3 minuti.

- È possibile annullare l'ordine di Data Box Heavy nel portale di Azure prima che venga elaborato. Dopo l'elaborazione, l'ordine non può essere annullato. L'ordine progredisce fino a raggiungere la fase di completamento. Per annullare l'ordine, passare a **Panoramica** e fare clic su **Annulla** dalla barra dei comandi.

- È possibile eliminare l'ordine quando lo stato risulta **Completato** oppure **Annullato** nel portale di Azure. Per eliminare l'ordine, passare a **Panoramica** e fare clic su **Elimina** dalla barra dei comandi.

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stato illustrato come distribuire un dispositivo Data Box Heavy per facilitare l'importazione dei dati in Azure. Per altre informazioni sulla gestione di Azure Data Box Heavy, passare all'esercitazione seguente:

> [!div class="nextstepaction"]
> [Usare il portale di Azure per amministrare Data Box Heavy](data-box-portal-admin.md)

::: zone-end
