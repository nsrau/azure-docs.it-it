---
title: Panoramica di Microsoft Azure Data Box Gateway | Microsoft Docs
description: Descrive Azure Data Box Gateway, una soluzione di archiviazione di appliance virtuale che consente di trasferire dati in Azure
services: databox
author: alkohli
ms.service: databox
ms.topic: overview
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: 4f1ab6d955c81ce6f7b141eef42341f43bb379f6
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/12/2018
ms.locfileid: "49165318"
---
# <a name="what-is-azure-data-box-gateway-preview"></a>Che cos'è Azure Data Box Gateway (anteprima)? 

Azure Data Box Gateway è una soluzione di archiviazione che consente di inviare dati in Azure. Questo articolo fornisce una panoramica della soluzione Azure Data Box Gateway e dei vantaggi, delle funzionalità chiave e degli scenari in cui distribuire questo dispositivo. 

Data Box Gateway è un dispositivo virtuale basato su una macchina virtuale sottoposta a provisioning nell'ambiente virtualizzato o in hypervisor. Il dispositivo virtuale si trova in locale ed è possibile scrivere dati al suo interno mediante i protocolli NFS e SMB. Il dispositivo trasferisce quindi i dati in BLOB in blocchi, BLOB di pagine o File di Azure. 

> [!IMPORTANT]
> Data Box Gateway è in anteprima. Prima di distribuire la soluzione, leggere le [condizioni d'uso per l'anteprima](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="use-cases"></a>Casi d'uso

Data Box Gateway consente di trasferire i dati nel cloud, ad esempio un archivio cloud, il ripristino di emergenza o se è necessario elaborare i dati su scala cloud. Ecco i vari scenari in cui è possibile usare Data Box Gateway per il trasferimento dei dati.

- **Archiviazione cloud**: copiare centinaia di terabyte di dati in una risorsa di archiviazione di Azure usando Data Box Gateway in modo sicuro ed efficiente. I dati possono essere inseriti una sola volta o ripetutamente per scenari di archiviazione.

- **Aggregazione di dati**: aggregare i dati di più origini in un'unica posizione in Archiviazione di Azure per l'elaborazione e l'analisi dei dati.  

- **Integrazione con carichi di lavoro locali**: eseguire l'integrazione con carichi di lavoro locali, ad esempio backup e ripristino, che usano l'archiviazione cloud e necessitano dell'accesso locale per i file usati più di frequente. 

## <a name="benefits"></a>Vantaggi

Data Box Gateway offre i vantaggi seguenti:

- **Trasferimento semplice dei dati**: rende lo spostamento dei dati da e verso l'archiviazione di Azure semplice quanto l'uso di una condivisione di rete locale.  
- **Prestazioni elevate**: semplifica il trasporto di dati in rete con trasferimenti a prestazioni elevate da e verso Azure. 
- **Accesso rapido**: memorizza nella cache i file più recenti per un accesso rapido dei file locali.  
- **Uso della larghezza di banda limitata**: i dati possono essere scritti in Azure anche quando la rete è limitata per ridurre l'utilizzo durante le ore lavorative più intense.  

## <a name="key-capabilities"></a>Funzionalità principali

Data Box Gateway include le funzionalità seguenti:

|Funzionalità |DESCRIZIONE  |
|---------|---------|
|speed     | Larghezza di banda e trasferimento dei dati completamente automatizzati e ottimizzati.|
|Protocolli supportati     | Supporto per protocolli SMB e NFS standard per l'inserimento di dati. <br> Per altre informazioni sulle versioni supportate, vedere i [requisiti di sistema di Data Box Gateway](data-box-gateway-system-requirements.md).|
|Accesso ai dati     | Indirizzare l'accesso ai dati dai BLOB del servizio di archiviazione di Azure e da File di Azure usando le API cloud per un'ulteriore elaborazione dei dati nel cloud.|
|Accesso rapido     | Cache locale nel dispositivo per l'accesso rapido dei file usati più di recente.|
|Caricamento offline     | La modalità disconnessa supporta scenari di caricamento offline.|
|Aggiornamento dati     | Possibilità di aggiornare i file locali con quelli più recenti nel cloud.|
|Crittografia    | Supporto BitLocker per crittografare i dati in locale e proteggere tramite *https* il trasferimento dei dati nel cloud       |
|Resilienza     | Resilienza di rete integrata        |


## <a name="specifications"></a>Specifiche

Il dispositivo virtuale Data Box Gateway presenta le specifiche seguenti:

| Specifiche                                          | DESCRIZIONE              |
|---------------------------------------------------------|--------------------------|
| Processori virtuali (core)   | Minimo 4 |            
| Memoria  | Minimo 8 GB|
| Disponibilità|Nodo singolo|
| Dischi| Disco del sistema operativo: 250 GB <br> Disco dati: almeno 2 TB, con thin provisioning e supportato da unità SSD|
| Interfacce di rete|1 o più interfacce di rete virtuali|
| Protocolli di condivisione file nativi|SMB e NFS  |
| Sicurezza| Autenticazione per sbloccare l'accesso al dispositivo e ai dati <br> Dati in movimento crittografati mediante crittografia AES a 256 bit|
| Gestione| Interfaccia utente Web locale: configurazione iniziale, diagnostica e risparmio energia del dispositivo <br> Portale di Azure: gestione quotidiana dei dispositivi Data Box Gateway       |


## <a name="components"></a>Componenti

La soluzione Data Box Gateway è costituita dalla risorsa Data Box Gateway, dal dispositivo virtuale Data Box Gateway e da un'interfaccia utente Web locale.

* **Dispositivo virtuale Data Box Gateway**: un dispositivo basato su una macchina virtuale sottoposta a provisioning nell'ambiente virtualizzato o in hypervisor, che consente di inviare dati ad Azure. 
    
* **Risorsa Data Box Gateway**: una risorsa del portale di Azure che consente di gestire un dispositivo Data Box Gateway da un'interfaccia Web accessibile da posizioni geografiche diverse. Usare la risorsa Data Box Gateway per creare e gestire risorse, visualizzare e gestire dispositivi e avvisi e gestire condivisioni.  

    <!--![The Data Box Gateway service in Azure portal](media/data-box-overview/data-box-Gateway-service1.png)-->

    <!--For more information, go to [Use the Data Box Gateway service to administer your Data Box Gateway device](data-box-gateway-portal-ui-admin.md).-->

* **Interfaccia utente web locale di Data Box**: usare l'interfaccia utente Web locale per eseguire la diagnostica, arrestare e riavviare il dispositivo Data Box Gateway, visualizzare i log di copia e contattare il supporto tecnico Microsoft per inviare una richiesta di servizio.

    <!--![The Data Box Gateway local web UI](media/data-box-gateway-overview/data-box-gateway-local-web-ui.png)-->

    <!-- For information about using the web-based UI, go to [Use the web-based UI to administer your Data Box](data-box-gateway-portal-ui-admin.md).-->


## <a name="region-availability"></a>Aree di disponibilità

Il dispositivo fisico Data Box Edge, la risorsa di Azure e l'account di archiviazione di destinazione in cui trasferire i dati non devono necessariamente trovarsi tutti nella stessa area.

- **Disponibilità della risorsa**: per questa versione, la risorsa Data Box Edge è disponibile nelle aree seguenti:
    - **Stati Uniti**: Stati Uniti occidentali 2 e Stati Uniti orientali
    - **Unione europea**: Europa occidentale
    - **Asia Pacifico**: Asia sud-orientale

- **Account di archiviazione di destinazione**: gli account che archiviano i dati sono disponibili in tutte le aree di Azure. 

    Le aree in cui gli account archiviano i dati Data Box devono essere nelle vicinanze del dispositivo per garantire prestazioni ottimali. Un account di archiviazione lontano dal dispositivo si traduce in lunghe latenze e prestazioni ridotte. 


## <a name="next-steps"></a>Passaggi successivi

- Vedere i [requisiti di sistema di Data Box Gateway](data-box-gateway-system-requirements.md).
- Comprendere i [limiti di Data Box Gateway](data-box-gateway-limits.md).
- Distribuire [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) nel portale di Azure.




