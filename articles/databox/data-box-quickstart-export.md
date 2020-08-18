---
title: 'Avvio rapido: Esportare i dati per Microsoft Azure Data Box'
description: Informazioni su come esportare rapidamente i dati del dispositivo Azure Data Box nel portale di Azure
services: databox
author: priestlg
ms.service: databox
ms.subservice: pod
ms.topic: quickstart
ms.date: 07/17/2020
ms.author: v-grpr
ms.localizationpriority: high
ms.openlocfilehash: fbe14151845d19dd1d0f2a4069afb2badb125077
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88122242"
---
# <a name="quickstart-get-started-with-azure-data-box-to-export-data-from-azure"></a>Avvio rapido: Iniziare a usare Azure Data Box per esportare dati da Azure

Questo argomento di avvio rapido illustra come esportare i dati da Azure nel percorso locale usando il portale di Azure. I passaggi includono il cablaggio, la configurazione e la copia dei dati da Azure. La guida introduttiva viene eseguita nel portale di Azure e nell'interfaccia utente Web locale del dispositivo.

Per istruzioni dettagliate sulla distribuzione e sul monitoraggio, vedere [Esercitazione: Creare l'ordine di esportazione per Azure Data Box](data-box-deploy-export-ordered.md)

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare:

* La sottoscrizione usata per il servizio Data Box sia di uno dei tipi seguenti:
  * Contratto Enterprise Microsoft. Altre informazioni sui [contratti Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/).
  * Cloud Solution Provider (CSP). Altre informazioni sul [programma Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview).
  * Microsoft Azure Sponsorship Altre informazioni sul [programma Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/).

* Si abbia accesso alla sottoscrizione come proprietario o collaboratore per creare un ordine Data Box.
* Esaminare le [linee guida di sicurezza per Data Box](data-box-safety.md).
* Assicurarsi di avere un dispositivo Azure Data Box per spostare i dati nel percorso locale. Il computer host deve:
  * Eseguire un [sistema operativo supportato](data-box-system-requirements.md).
  * Essere connesso a una rete ad alta velocità. È consigliabile avere una connessione di almeno 10 GbE. In assenza di una connessione a questa velocità è possibile usare un collegamento dati a 1 GbE, ma la velocità dell'operazione di copia ne risentirà.
* Si abbia accesso a una superficie piana su cui posizionare il Data Box. Per posizionare il dispositivo su uno scaffale standard, è necessario uno slot 7U nel rack del data center. È possibile posizionare il dispositivo in piano o in verticale nel rack.
* Assicurarsi di avere i cavi seguenti per collegare il Data Box al computer host.
  * Due cavi in rame SFP+ Twinax a 10 GbE (da usare con le interfacce di rete DATA 1, DATA 2)
  * Un cavo di rete RJ-45 CAT 6 (da usare con l'interfaccia di rete di gestione MGMT)
  * Un cavo di rete RJ-45 CAT 6A oppure RJ-45 CAT 6 (da usare con l'interfaccia di rete DATA 3 configurata rispettivamente come 10 Gbps o 1 Gbps)

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="order"></a>JSON

Questo passaggio richiede circa 10 minuti.

1. Creare una nuova risorsa Azure Data Box nel portale di Azure.
2. Selezionare una sottoscrizione esistente abilitata per questo servizio e scegliere **Export to Azure** (Esporta in Azure) come tipo di trasferimento. Specificare l'**area di Azure di origine** in cui risiedono i dati e il **paese di destinazione** per il trasferimento dei dati.
3. Selezionare **Data Box**. La capacità massima utilizzabile è di 80 TB ed è possibile creare più ordini per dimensioni dei dati più grandi.
4. Selezionare **Aggiungi account di archiviazione e tipo di esportazione** e quindi **Selezionare l'opzione di esportazione**.
5. Immettere i dettagli dell'ordine e le informazioni per la spedizione. Se il servizio è disponibile nella propria area, specificare gli indirizzi di posta elettronica per le notifiche, controllare il riepilogo e quindi creare l'ordine.

Dopo aver creato l'ordine, il dispositivo verrà preparato per la spedizione.

## <a name="cable"></a>Cablare

Questo passaggio richiede circa 10 minuti.

Quando si riceve il dispositivo Data Box, procedere come segue per cablare il dispositivo e connettersi ad esso e accenderlo. Questo passaggio richiede circa 10 minuti.

1. Qualora risulti che il dispositivo sia stato manomesso o danneggiato, non procedere. Contattare il supporto tecnico Microsoft per richiedere un dispositivo sostitutivo.
2. Prima di cablare il dispositivo, assicurarsi di avere i cavi seguenti:

   * Cavo di alimentazione a terra da 10 A o superiore (incluso) con un connettore IEC60320 C-13 a un'estremità per la connessione al dispositivo.
   * Un cavo di rete RJ-45 CAT 6 (da usare con l'interfaccia di rete di gestione MGMT)
   * Due cavi in rame SFP+ Twinax a 10 GbE (da usare con interfacce di rete DATA 1, DATA 2 a 10 Gbps)
   * Un cavo di rete RJ-45 CAT 6A oppure RJ-45 CAT 6 (da usare con l'interfaccia di rete DATA 3 configurata rispettivamente come 10 Gbps o 1 Gbps)

3. Rimuovere e posizionare il dispositivo su una superficie piana.

4. Cablare il dispositivo come illustrato di seguito.  

    ![Backplane cablato di un dispositivo Data Box](media/data-box-deploy-set-up/data-box-cabled-dhcp.png)  

    1. Collegare il cavo di alimentazione al dispositivo.
    2. Usare il cavo di rete RJ-45 CAT 6 per connettere il computer host alla porta MGMT sul dispositivo.
    3. Usare il cavo di rame SFP+ Twinax per connettere almeno una interfaccia di rete a 10 Gbps (preferita rispetto a quella a 1 Gbps), DATA 1 o DATA 2 per i dati.
    4. Accendere il dispositivo. Il pulsante di alimentazione è sul pannello anteriore del dispositivo.

## <a name="connect"></a>Connessione

Questa operazione richiede circa 5-7 minuti.

1. Per ottenere la password del dispositivo, passare a **Generale** > **Dettagli dispositivo** nel [portale di Azure](https://portal.azure.com).
2. Assegnare un indirizzo IP statico 192.168.100.5 e la subnet 255.255.255.0 alla scheda Ethernet nel computer usato per connettersi al dispositivo Data Box. Accedere all'interfaccia utente Web locale del dispositivo all'indirizzo `https://192.168.100.10`. La connessione potrebbe richiedere fino a 5 minuti dopo l'accensione del dispositivo.
3. Eseguire l'accesso usando la password dal portale di Azure. Viene visualizzato un errore in cui si indica che si è verificato un problema con il certificato di sicurezza del sito Web. Seguire le istruzioni specifiche del browser per passare alla pagina Web.
4. Per impostazione predefinita, le impostazioni di rete per l'interfaccia dati a 10 Gbps (o 1 Gbps) sono configurate come DHCP. Se necessario, è possibile configurare questa interfaccia come statica e specificare un indirizzo IP.

## <a name="copy-data"></a>Copiare i dati

Il tempo per completare questa operazione dipende dalla dimensione dei dati e della velocità della rete.

1. Se si usa un client Windows, usare uno strumento di copia file compatibile con SMB, ad esempio Robocopy. Per l'host NFS, usare il comando `cp` oppure `rsync` per copiare i dati. Per altre informazioni su come usare Robocopy per copiare i dati, visitare [Robocopy](https://technet.microsoft.com/library/ee851678.aspx).
2. Connettersi alle condivisioni del dispositivo e avviare la copia dei dati nel computer host.
<!-- 1. Connect to the device shares using the path:`\\<IP address of your device>\ShareName`. To get the share access credentials, go to the **Connect & copy** page in the local web UI of the Data Box. --> 

## <a name="ship-to-azure"></a>Spedire ad Azure

Questa operazione richiede circa 10-15 minuti.

1. Passare alla pagina **Prepara per la spedizione** nell'interfaccia utente Web locale e avviare la preparazione della spedizione.
2. Disattivare il dispositivo dall'interfaccia utente Web locale. Rimuovere i cavi dal dispositivo.
3. Avvolgere il cavo di alimentazione fornito con il dispositivo e assicurarlo al retro del dispositivo.
4. L'etichetta indirizzo del reso deve essere visibile sullo schermo a inchiostro elettronico. Se l'etichetta non viene visualizzata sul display E-ink, passare a **Panoramica** > **Scarica etichetta di spedizione** nel portale di Azure. Scaricare l'etichetta di spedizione e applicarla sul dispositivo.
5. Per restituire il dispositivo, pianificare un ritiro con il corriere di zona.
6. Dopo che il Data Box è stato ritirato ed esaminato dal corriere, lo stato dell'ordine nel portale diventa **Ritirato**. Viene visualizzato anche un ID di traccia.

## <a name="clean-up-resources"></a>Pulire le risorse

Il completamento di questo passaggio richiede 2-3 minuti.

* È possibile annullare l'ordine di Data Box nel portale di Azure prima che venga elaborato. Dopo l'elaborazione, l'ordine non può essere annullato. L'ordine progredisce fino a raggiungere la fase di completamento. Per annullare l'ordine, passare a **Panoramica** e selezionare **Annulla** dalla barra dei comandi.

* È possibile eliminare l'ordine quando lo stato risulta **Completato** oppure **Annullato** nel portale di Azure. Per eliminare l'ordine, passare a **Panoramica** e selezionare **Elimina** dalla barra dei comandi.

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stato illustrato come distribuire un ordine di esportazione da Azure in un dispositivo Azure Data Box. Per altre informazioni sulla gestione di Azure Data Box, passare all'esercitazione seguente:

> [!div class="nextstepaction"]
> [Use the Azure portal to administer Data Box](data-box-portal-admin.md) (Usare il portale di Azure per gestire Data Box)
