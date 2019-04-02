---
title: Panoramica di Microsoft Azure Data Box Gateway | Microsoft Docs
description: Descrive Azure Data Box Gateway, una soluzione di archiviazione di appliance virtuale che consente di trasferire dati in Azure
services: databox
author: alkohli
ms.service: databox
ms.topic: overview
ms.date: 03/20/2019
ms.author: alkohli
ms.openlocfilehash: e7a65a6b49544783ed3f40194e2338540819536b
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58400966"
---
# <a name="what-is-azure-data-box-gateway"></a>Informazioni su Azure Data Box Gateway

Azure Data Box Gateway è una soluzione di archiviazione che consente di inviare dati in Azure. Questo articolo fornisce una panoramica della soluzione Azure Data Box Gateway e dei vantaggi, delle funzionalità chiave e degli scenari in cui distribuire questo dispositivo.

Data Box Gateway è un dispositivo virtuale basato su una macchina virtuale sottoposta a provisioning nell'ambiente virtualizzato o in hypervisor. Il dispositivo virtuale si trova in locale ed è possibile scrivere dati al suo interno mediante i protocolli NFS e SMB. Il dispositivo trasferisce quindi i dati in BLOB in blocchi, BLOB di pagine o File di Azure.

## <a name="use-cases"></a>Casi d'uso

Data Box Gateway consente di trasferire i dati nel cloud, ad esempio un archivio cloud, il ripristino di emergenza o se è necessario elaborare i dati su scala cloud. Ecco i vari scenari in cui è possibile usare Data Box Gateway per il trasferimento dei dati.

- **Archiviazione cloud**: copiare centinaia di terabyte di dati in una risorsa di archiviazione di Azure usando Data Box Gateway in modo sicuro ed efficiente. I dati possono essere inseriti una sola volta o ripetutamente per scenari di archiviazione.

- **Inserimento dati continuo**: inserire continuamente dati nel dispositivo per la copia nel cloud, indipendentemente dalle dimensioni dei dati. Man mano che i dati vengono scritti nel dispositivo gateway, questo li carica in Archiviazione di Azure.  

- **Trasferimento in blocco iniziale seguito da trasferimento incrementale**: usare Data Box per il trasferimento in blocco in modalità offline (seeding iniziale) e Data Box Gateway per i trasferimenti incrementali (feed continuo) tramite la rete.

## <a name="benefits"></a>Vantaggi

Data Box Gateway offre i vantaggi seguenti:

- **Trasferimento semplice dei dati**: rende lo spostamento dei dati da e verso l'archiviazione di Azure semplice quanto l'uso di una condivisione di rete locale.  
- **Prestazioni elevate**: semplifica il trasporto di dati in rete con trasferimenti a prestazioni elevate da e verso Azure.
- **Accesso veloce e velocità di inserimento dati elevate durante le ore lavorative**: Azure Data Box Gateway ha una cache locale che si definisce come dimensioni della capacità locale quando viene eseguito il provisioning del dispositivo virtuale. Le dimensioni del disco dati devono essere specificate in base ai [requisiti minimi del dispositivo virtuale](data-box-gateway-system-requirements.md#specifications-for-the-virtual-device). La cache locale offre i vantaggi seguenti:
    - La cache locale consente l'inserimento di dati con una frequenza elevata. Quando una quantità elevata di dati viene inserita durante le ore di punta, la cache può contenere i dati e caricarli nel cloud.
    - La cache locale consente un rapido accesso in lettura fino a una determinata soglia. Fino a quando il dispositivo non pieno al 50-60%, tutte le operazioni di lettura dal dispositivo sono accessibili dalla cache, rendendole più veloci. Quando lo spazio usato nel dispositivo supera questa soglia, il dispositivo inizia a rimuovere i file locali.
 
- **Uso della larghezza di banda limitata**: i dati possono essere scritti in Azure anche quando la rete è limitata per ridurre l'utilizzo durante le ore lavorative più intense.  

## <a name="key-capabilities"></a>Funzionalità principali

Data Box Gateway include le funzionalità seguenti:

|Funzionalità |DESCRIZIONE  |
|---------|---------|
|speed     | Larghezza di banda e trasferimento dei dati completamente automatizzati e ottimizzati.|
|Protocolli supportati     | Supporto per protocolli SMB e NFS standard per l'inserimento di dati. <br> Per altre informazioni sulle versioni supportate, vedere i [requisiti di sistema di Data Box Gateway](data-box-gateway-system-requirements.md).|
|Accesso ai dati     | Quando i dati inviati dal dispositivo si trovano nel cloud, possono essere ulteriormente modificati accedendo direttamente alle API cloud.|
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
| Memoria  |Minimo 8 GB|
| Disponibilità|Nodo singolo|
| Dischi|Disco sistema operativo: 250 GB <br> Disco dati: almeno 2 TB, con thin provisioning e supportato da unità SSD|
| Interfacce di rete |1 o più interfacce di rete virtuali|
| Protocolli di condivisione file nativi|SMB e NFS  |
| Security|Autenticazione per sbloccare l'accesso al dispositivo e ai dati <br> Dati in movimento crittografati mediante crittografia AES a 256 bit|
| Gestione|Interfaccia utente Web locale: configurazione iniziale, diagnostica e risparmio energia del dispositivo <br> Portale di Azure: gestione quotidiana dei dispositivi Data Box Gateway       |

## <a name="components"></a>Componenti

La soluzione Data Box Gateway è costituita dalla risorsa Data Box Gateway, dal dispositivo virtuale Data Box Gateway e da un'interfaccia utente Web locale.

- **Dispositivo virtuale Data Box Gateway**: un dispositivo basato su una macchina virtuale sottoposta a provisioning nell'ambiente virtualizzato o in hypervisor, che consente di inviare dati ad Azure.
    
- **Risorsa Data Box Gateway**: una risorsa del portale di Azure che consente di gestire un dispositivo Data Box Gateway da un'interfaccia Web accessibile da posizioni geografiche diverse. Usare la risorsa Data Box Gateway per visualizzare e gestire il dispositivo, le condivisioni, gli utenti e gli avvisi. Per altre informazioni, vedere l'articolo relativo alla [gestione con il portale di Azure](data-box-gateway-manage-shares.md).

- **Interfaccia utente Web locale di Data Box**: usare l'interfaccia utente Web locale per eseguire la diagnostica, arrestare e riavviare il dispositivo, generare un pacchetto per il supporto o contattare il supporto tecnico Microsoft per aprire una richiesta di servizio. Per altre informazioni, vedere l'articolo relativo alla [gestione con l'interfaccia utente Web locale](data-box-gateway-manage-access-power-connectivity-mode.md).

## <a name="region-availability"></a>Aree di disponibilità

Il dispositivo fisico Data Box Gateway, la risorsa di Azure e l'account di archiviazione di destinazione in cui si trasferiscono i dati non devono necessariamente trovarsi tutti nella stessa area.

- **Disponibilità della risorsa**: per questa versione, la risorsa Data Box Gateway è disponibile nelle aree elencate di seguito, che supportano il cloud pubblico.
    - **Stati Uniti**: Stati Uniti orientali
    - **Unione europea**: Europa occidentale
    - **Asia Pacifico**: Asia sud-orientale

    È possibile distribuire Data Box Gateway anche nel cloud di Azure per enti pubblici. Per altre informazioni, vedere [What is Azure Government?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome) (Informazioni su Azure per enti pubblici).

- **Account di archiviazione di destinazione**: gli account che archiviano i dati sono disponibili in tutte le aree di Azure.

    Le aree in cui gli account archiviano i dati Data Box devono essere nelle vicinanze del dispositivo per garantire prestazioni ottimali. Un account di archiviazione lontano dal dispositivo si traduce in lunghe latenze e prestazioni ridotte.


## <a name="next-steps"></a>Passaggi successivi

- Vedere i [requisiti di sistema di Data Box Gateway](data-box-gateway-system-requirements.md).
- Comprendere i [limiti di Data Box Gateway](data-box-gateway-limits.md).
- Distribuire [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) nel portale di Azure.

