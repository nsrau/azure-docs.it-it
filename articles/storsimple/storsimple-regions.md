---
title: "Disponibilità in base all'area StorSimple | Microsoft Docs"
description: Illustra le aree di Azure in cui sono disponibili i modelli di dispositivo StorSimple.
services: storsimple
documentationcenter: 
author: alkohli
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2017
ms.author: alkohli
ms.openlocfilehash: d47109d541a3df93d9234e27e53d1538f6bc4c6e
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2017
---
# <a name="available-regions-for-your-storsimple"></a>Aree disponibili per StorSimple

## <a name="overview"></a>Panoramica

I Data center di Azure operano in più aree geografiche in tutto il mondo per soddisfare le richieste dei clienti relative a prestazioni, requisiti e preferenze sulla posizione dei dati. Un'area geografica di Azure è un'area definita del mondo che include almeno un'area di Azure. Un'area di Azure all'interno di un'area geografica include uno o più data center.

La scelta di un'area di Azure è molto importante ed è influenzata da fattori quali la sovranità e residenza dei dati, la disponibilità del servizio, le prestazioni, il costo e la ridondanza. Per altre informazioni su come scegliere un'area, vedere [Qual è l'area di Azure più adatta alle mie esigenze?](https://azure.microsoft.com/overview/datacenters/how-to-choose/)

Per la soluzione StorSimple, la scelta dell'area è determinata dai seguenti fattori:

- Aree in cui è disponibile il servizio Gestione dispositivi StorSimple.
- I paesi in cui è disponibile il dispositivo StorSimple fisico, cloud o virtuale è disponibile.
- Le aree in cui gli account di archiviazione che contengono i dati StorSimple dovrebbero essere disponibili per l'ottimizzazione delle prestazioni.

Questa esercitazione illustra le aree di disponibilità per il servizio Gestione dispositivi StorSimple, i dispositivi fisici in locale e i dispositivi cloud. Le informazioni contenute in questo articolo si applicano ai dispositivi StorSimple serie 8000 e 1200.

## <a name="region-availability-for-storsimple-device-manager-service"></a>Aree di disponibilità del servizio Gestione dispositivi StorSimple

Il servizio Gestione dispositivi StorSimple è attualmente supportato in 12 aree pubbliche e 2 aree di Azure per enti pubblici.

Un'area viene definita al momento della prima creazione del servizio Gestione dispositivi StorSimple. In generale, si sceglie un percorso più vicino all'area geografica in cui si vuole distribuire il dispositivo. Ma il dispositivo e il servizio possono anche essere distribuiti in luoghi diversi.

Ecco un elenco delle aree in cui il servizio Gestione dispositivi StorSimple è disponibile per il cloud pubblico di Azure e può essere distribuito.

![storsimple-device-manager-service-regions](./media/storsimple-region/storsimple-device-manager-service-regions.png)

Per il cloud di Azure per enti pubblici, il servizio Gestione dispositivi StorSimple è disponibile nei data center statunitensi dell'Iowa e della Virginia.

## <a name="region-availability-for-data-stored-in-storsimple"></a>Aree di disponibilità per i dati archiviati StorSimple

I dati StorSimple vengono fisicamente archiviati negli account di archiviazione di Azure e questi account sono disponibili in tutte le aree di Azure. Quando si crea un account di archiviazione di Azure, viene scelta la posizione principale dell'account di archiviazione che determina l'area in cui risiedono i dati.

La prima volta che si crea un servizio Gestione dispositivi StorSimple e lo si associa a un account di archiviazione, il servizio Gestione dispositivi StorSimple e la risorsa di archiviazione di Azure possono trovarsi in due posizioni distinte. In tal caso, è necessario creare l'account di Gestione dispositivi StorSimple e di Archiviazione di Azure separatamente.

In generale, scegliere l'area più vicina al servizio per l'account di archiviazione. Tuttavia, l'area più vicina di Microsoft Azure potrebbe non risultare l'area con latenza più bassa. È la latenza che determina le prestazioni del servizio di rete e pertanto le prestazioni della soluzione. Pertanto, se si sceglie un account di archiviazione in un'area diversa, è importante sapere quali sono le latenze tra il servizio e l'area associata al proprio account di archiviazione.

Se si usa un'appliance cloud StorSimple, è in genere consigliabile posizionare il servizio e l'account di archiviazione associato nella stessa area. Gli account di archiviazione posti in aree diverse possono causare una riduzione delle prestazioni.

## <a name="availability-of-storsimple-device"></a>Disponibilità del dispositivo StorSimple

A seconda del modello, i dispositivi StorSimple possono essere disponibili in aree geografiche o paesi diversi.

### <a name="storsimple-physical-device-models-81008600"></a>Dispositivo fisico StorSimple (modelli 8100/8600)

Se si utilizza un dispositivo fisico StorSimple 8100 o 8600, il dispositivo è disponibile nei seguenti paesi.

| #  | Paese        | #  | Paese     | #  | Paese      | #  | Paese              |
|----|----------------|----|-------------|----|--------------|----|----------------------|
| 1  | Australia      | 16 | Hong Kong - R.A.S.   | 31 | Nuova Zelanda  | 46 | Sud Africa         |
| 2  | Austria        | 17 | Ungheria     | 32 | Nigeria      | 47 | Corea del Sud          |
| 3  | Bahrain        | 18 | Islanda     | 33 | Norvegia       | 48 | Spagna                |
| 4  | Belgio        | 19 | India       | 34 | Perù         | 49 | Sri Lanka            |
| 5  | Brasile         | 20 | Indonesia   | 35 | Filippine  | 50 | Svezia               |
| 6  | Canada         | 21 | Irlanda     | 36 | Polonia       | 51 | Svizzera          |
| 7  | Cile          | 22 | Israele      | 37 | Portogallo     | 52 | Taiwan               |
| 8  | Colombia       | 23 | Italia       | 38 | Portorico  | 53 | Thailandia             |
| 9  | Repubblica ceca | 24 | Giappone       | 39 | Qatar        | 54 | Turchia               |
| 10 | Danimarca        | 25 | Kenya       | 40 | Romania      | 55 | Ucraina              |
| 11 | Egitto          | 26 | Kuwait      | 41 | Russia       | 56 | Emirati Arabi Uniti |
| 12 | Finlandia        | 27 | Macao       | 42 | Arabia Saudita | 57 | Regno Unito       |
| 13 | Francia         | 28 | Malaysia    | 43 | Singapore    | 58 | Stati Uniti        |
| 14 | Germania        | 29 | Messico      | 44 | Slovacchia     | 59 | Vietnam              |
| 15 | Grecia         | 30 | Paesi Bassi | 45 | Slovenia     | 60 | Croazia              |

Questo elenco viene modificato quando si aggiungono nuovi paesi. Per un elenco più aggiornato dei luoghi geografici, vedere l'appendice dei termini della matrice di archiviazione nelle [condizioni del prodotto](https://www.microsoft.com/en-us/Licensing/product-licensing).

Microsoft può fornire l'hardware fisico e la sostituzione dei pezzi di ricambio dell'hardware per StorSimple per le aree geografiche nell'elenco precedente.

> [!IMPORTANT]
> Non posizionare un dispositivo fisico StorSimple in un'area in cui StorSimple non è supportato. Microsoft non potrà fornire eventuali parti di ricambio in paesi in cui StorSimple non è supportato.

### <a name="storsimple-cloud-appliance-models-80108020"></a>Appliance cloud StorSimple (modelli 8010/8020)

Se si utilizza un'appliance cloud StorSimple 8010 o 8020, il dispositivo è disponibile e supportato in tutte le aree in cui la macchina virtuale è supportata. Il modello 8010 usa una macchina virtuale _Standard_A3_ che è supportata in tutte le aree di Azure.

Il modello 8020 usa l'archiviazione premium e la macchina virtuale _Standard_DS3_ per creare un'applicazione cloud. Il modello 8020 è sopportato in tutte le aree di Azure che supportano l'archiviazione premium e le macchine virtuali _Standard_DS3_ di Azure. Usare [questo elenco](https://azure.microsoft.com/regions/services/) per verificare se nella propria area sono disponibili sia le **macchine virtuali serie DS** che l'**archiviazione su disco**.

### <a name="storsimple-virtual-array-model-1200"></a>Array virtuale StorSimple (modello 1200)

Se si usa la serie 1200 dell'array virtuale StorSimple, l'immagine del disco virtuale è supportata in tutte le aree di Azure.

## <a name="next-steps"></a>Passaggi successivi

* Ulteriori informazioni sul [costo dei vari modelli di StorSimple](https://azure.microsoft.com/pricing/calculator/#storsimple2).
* Ulteriori informazioni sulla [gestione dell'account di archiviazione di StorSimple](storsimple-8000-manage-storage-accounts.md).
* Altre informazioni su come [usare il servizio Gestione dispositivi StorSimple per amministrare un dispositivo StorSimple](storsimple-8000-manager-service-administration.md).
