---
title: Configurare un Lab SOLIDWORKs per la progettazione con Azure Lab Services | Microsoft Docs
description: Informazioni su come configurare un Lab per corsi di progettazione con SOLIDWORKs.
author: nicolela
ms.topic: article
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: dc6fdadbdfdbdd1d32f640e356a67841187a83c9
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94651805"
---
# <a name="set-up-a-lab-for-engineering-classes-using-solidworks"></a>Configurare un Lab per le classi di progettazione con SOLIDWORKs

[SolidWorks](https://www.solidworks.com/) offre un ambiente CAD (computer-aided design) 3D per la modellazione di oggetti solidi e viene usato in diversi tipi di campi di progettazione.  Con SOLIDWORKs, i tecnici possono creare, visualizzare, simulare e documentare facilmente i loro progetti.

Un'opzione di licenza usata comunemente dalle università è la gestione delle licenze di rete di SOLIDWORKs.   Con questa opzione, gli utenti condividono un pool di licenze gestite da un server licenze.  Questo tipo di licenza viene a volte definita licenza "mobile" perché è necessario disporre di licenze sufficienti per il numero di utenti simultanei.  Quando un utente utilizza SOLIDWORKs, la relativa licenza torna al pool di licenze gestito centralmente, in modo che possa essere riutilizzato da un altro utente.

Questo articolo illustra come configurare una classe che usa SOLIDWORKs 2019 e le licenze di rete.

## <a name="license-server"></a>Server licenze

SOLIDWORKs Network Licensing richiede che SolidNetWork License Manager sia installato e attivato nel server licenze.  Questo server licenze si trova in genere nella rete locale o in una rete privata in Azure.  Per ulteriori informazioni su come configurare Gestione licenze SolidNetWork nel server, vedere [installazione e attivazione di un gestore licenze](https://help.solidworks.com/2019/English/Installation/install_guide/t_installing_snl_lic_mgr.htm) nella Guida all'installazione di SolidWorks.  Quando si configura questa impostazione, ricordare il **numero di porta** e il numero di [**serie**](https://help.solidworks.com/2019/english/installation/install_guide/r_hid_state_serial_number.htm) utilizzati perché saranno necessari nei passaggi successivi.

Dopo aver configurato il server licenze, è necessario eseguire il peering della [rete virtuale (VNet)](./how-to-connect-peer-virtual-network.md) all' [account Lab](./tutorial-setup-lab-account.md).  Il peering di rete deve essere eseguito prima di creare il Lab in modo che le macchine virtuali del Lab possano accedere al server licenze e viceversa.

> [!NOTE]
> È necessario verificare che le porte appropriate siano aperte nei firewall per consentire la comunicazione tra le macchine virtuali del Lab e il server licenze.  Vedere ad esempio le istruzioni sulla [modifica delle porte del computer di gestione licenze per Windows Firewall](http://help.solidworks.com/2019/english/installation/install_guide/t_mod_ports_on_lic_mgr_for_firewall.htm) che illustrano come aggiungere regole in ingresso e in uscita al firewall del server licenze.  Potrebbe anche essere necessario aprire le porte per le macchine virtuali del Lab.  Per altre informazioni su questo argomento, vedere la procedura descritta nell'articolo sulle [impostazioni del firewall per Labs](./how-to-configure-firewall-settings.md) , incluso come ottenere l'indirizzo IP pubblico del Lab.

## <a name="lab-configuration"></a>Configurazione del lab

Per configurare questo Lab, è necessario disporre di una sottoscrizione di Azure e di un account Lab per iniziare. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare. Una volta ottenuto una sottoscrizione di Azure, è possibile creare un nuovo account Lab in Azure Lab Services. Per ulteriori informazioni sulla creazione di un nuovo account Lab, vedere l'esercitazione su [come configurare un account Lab](./tutorial-setup-lab-account.md). È anche possibile usare un account lab esistente.

### <a name="lab-account-settings"></a>Impostazioni dell'account Lab

Abilitare le impostazioni descritte nella tabella seguente per l'account Lab. Per altre informazioni su come abilitare le immagini del Marketplace, vedere l'articolo su [come specificare le immagini del Marketplace disponibili per gli autori di Lab](./specify-marketplace-images.md).

| Impostazione dell'account lab | Istruzioni |
| ------------------- | ------------ |
|Immagine del Marketplace| Abilitare l'immagine Windows 10 Pro da usare nell'account Lab.|

> [!NOTE]
> Oltre a Windows 10, SOLIDWORKs supporta le altre versioni di Windows.  Per informazioni dettagliate, vedere [requisiti di sistema per SolidWorks](https://www.solidworks.com/sw/support/SystemRequirements.html) .

### <a name="lab-settings"></a>Impostazioni del lab

Usare le impostazioni nella tabella seguente quando si configura un Lab della classe. Per altre informazioni su come creare un Lab per le aule, vedere l'esercitazione sull'impostazione di un Lab per la classe.

| Impostazioni del lab | Valore/istruzioni |
| ------------ | ------------------ |
|Dimensioni della macchina virtuale| **Piccola GPU (visualizzazione)**.  Questa macchina virtuale è ideale per la visualizzazione remota, lo streaming, i giochi e la codifica tramite Framework come OpenGL e DirectX.|  
|Immagine di macchina virtuale| Windows 10 Pro|

> [!NOTE]
> Le dimensioni della macchina virtuale di **piccole dimensioni (visualizzazione)** sono configurate per consentire un'esperienza grafica a prestazioni elevate.  Per altre informazioni sulle dimensioni della macchina virtuale, vedere l'articolo su [come configurare un Lab con GPU](./how-to-setup-lab-gpu.md).

> [!WARNING]
> Non dimenticare di eseguire il [peering della rete virtuale](https://www.mathworks.com/support/requirements/matlab-system-requirements.html) per l'account Lab alla rete virtuale per il server licenze **prima** di creare il Lab.

## <a name="template-virtual-machine-configuration"></a>Configurazione della macchina virtuale modello

I passaggi descritti in questa sezione illustrano come configurare la macchina virtuale modello scaricando i file di installazione di SOLIDWORKs e installando il software client:

1. Avviare la macchina virtuale modello e connettersi al computer tramite RDP.

1. Scaricare i file di installazione per il software client SOLIDWORKs. Sono disponibili due opzioni per il download:
   - Scaricare da [SolidWorks Portal](https://login.solidworks.com/nidp/idff/sso?id=cpenglish&sid=1&option=credential&sid=1&target=https%3A%2F%2Fcustomerportal.solidworks.com%2F).
   - Scaricare da una directory in un server.  Se è stata usata questa opzione, è necessario assicurarsi che il server sia accessibile dalla macchina virtuale modello.  Questo server, ad esempio, potrebbe trovarsi nella stessa rete virtuale con peering con l'account Lab.
  
    Per informazioni dettagliate, vedere [installazione nei singoli computer nella](http://help.solidworks.com/2019/english/Installation/install_guide/c_installing_on_individual_computers.htm?id=fc149e8a968a422a89e2a943265758d3#Pg0) Guida all'installazione di SolidWorks in SolidWorks.

1. Una volta scaricati i file di installazione, installare il software client utilizzando SOLIDWORKs Installation Manager. Vedere i dettagli sull' [installazione di un client licenze](http://help.solidworks.com/2019/english/installation/install_guide/t_installing_snl_license_client.htm) in SolidWorks install guide.

    > [!NOTE]
    > Nella finestra di dialogo **Aggiungi server** verrà richiesto il **numero di porta** utilizzato per il server licenze e il nome o l'indirizzo IP del server licenze.

## <a name="cost"></a>Cost

Verrà ora trattata una possibile stima dei costi per questa classe. Questa stima non include il costo di esecuzione del server licenze. Verrà usata una classe di 25 studenti. Sono previste 20 ore di tempo della classe pianificata. Ogni studente riceve inoltre una quota di 10 ore per il lavoro o assegnazioni al di fuori dell'orario di classe pianificato. Le dimensioni della macchina virtuale scelte sono **small GPU (visualizzazione)**, ovvero 160 unità Lab.

25 studenti \* (20 ore pianificate + 10 ore di quota) \* 160 unità Lab * 0,01 USD all'ora = 1200,00 USD

>[!IMPORTANT]
> La stima dei costi è solo a scopo esemplificativo.  Per informazioni aggiornate sui prezzi, vedere [Azure Lab Services prezzi](https://azure.microsoft.com/pricing/details/lab-services/).  

## <a name="next-steps"></a>Passaggi successivi

I passaggi successivi sono comuni per la configurazione di qualsiasi Lab.

- [Creare e gestire un modello](how-to-create-manage-template.md)
- [Aggiungere utenti](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Impostare la quota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Impostare una pianificazione](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Collegamenti per la registrazione tramite posta elettronica agli studenti](how-to-configure-student-usage.md#send-invitations-to-users)