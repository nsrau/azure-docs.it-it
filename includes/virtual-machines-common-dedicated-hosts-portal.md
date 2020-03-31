---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/10/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 427117fe47294a1db1fa8d3fa1e46ee1efb91b4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79129288"
---
## <a name="limitations"></a>Limitazioni

- I set di scalabilità delle macchine virtuali non sono attualmente supportati in host dedicati.
- Le dimensioni e i tipi di hardware disponibili per gli host dedicati variano in base all'area geografica. Per ulteriori informazioni, consulta la [pagina dei prezzi](https://aka.ms/ADHPricing) host.

## <a name="create-a-host-group"></a>Creare un gruppo host

Un **gruppo host** è una nuova risorsa che rappresenta una raccolta di host dedicati. Creare un gruppo host in un'area e una zona di disponibilità e aggiungervi host. Quando si pianifica la disponibilità elevata, sono disponibili opzioni aggiuntive. È possibile utilizzare una o entrambe le seguenti opzioni con gli host dedicati: 
- Estendere più zone di disponibilità. In questo caso, è necessario disporre di un gruppo host in ciascuna delle zone che si desidera utilizzare.
- Estendetevi su più domini di errore mappati a rack fisici. 
 
In entrambi i casi, è necessario fornire il conteggio dei domini di errore per il gruppo host. Se non si desidera estendere i domini di errore nel gruppo, utilizzare un conteggio dei domini di errore pari a 1. 

È inoltre possibile decidere di utilizzare sia le zone di disponibilità che i domini di errore. 

In questo esempio verrà creato un gruppo host usando 1 zona di disponibilità e 2 domini di errore. 


1. Aprire il [portale di](https://portal.azure.com)Azure .
1. Selezionare **Crea una risorsa** nell'angolo superiore sinistro.
1. Cercare **Gruppo host** e quindi selezionare **Gruppi host** nei risultati.
1. Nella pagina **Gruppi host** selezionare **Crea**.
1. Selezionare la sottoscrizione che si vuole usare e quindi selezionare **Crea nuova** per creare un nuovo gruppo di risorse.
1. Digitare *myDedicatedHostsRG* come **nome** e quindi selezionare **OK**.
1. Per **Nome gruppo host**digitare *myHostGroup*.
1. In **Località** selezionare **Stati Uniti orientali**.
1. Per **Zona di disponibilità**, selezionare **1**.
1. Per **Conteggio domini errori**, selezionare **2**.
1. Selezionare **Revisione e creare** e quindi attendere la convalida.
1. Dopo aver visualizzato il messaggio **Convalida superata,** selezionare **Crea** per creare il gruppo host.

La creazione del gruppo host dovrebbe richiedere solo pochi istanti.

## <a name="create-a-dedicated-host"></a>Creare un host dedicatoCreate a dedicated host

Creare ora un host dedicato nel gruppo host. Oltre a un nome per l'host, è necessario fornire lo SKU per l'host. Lo SKU host acquisisce la serie di macchine virtuali supportate e la generazione dell'hardware per l'host dedicato.

Per altre informazioni sugli SKU e sui prezzi dell'host, vedere [Prezzi dell'host dedicato di Azure.For more](https://aka.ms/ADHPricing)information about the host SKUs and pricing, see Azure Dedicated Host pricing .

Se imposti un numero di domini di errore per il tuo gruppo host, ti verrà chiesto di specificare il dominio di errore per il tuo host.  

1. Selezionare **Crea una risorsa** nell'angolo superiore sinistro.
1. Cercare **Host dedicato** e quindi selezionare **Host dedicati** dai risultati.
1. Nella pagina **Host dedicati** selezionare **Crea**.
1. Selezionare l'abbonamento che si desidera utilizzare.
1. Selezionare *myDedicatedHostsRG* come **gruppo di risorse**.
1. In **Dettagli istanza**digitare *myHost* come **Nome** e selezionare Stati *Uniti orientali* per la posizione.
1. In **Profilo hardware**selezionare Famiglia Standard *Es3 - Tipo 1* per la famiglia **Dimensioni**, selezionare *myHostGroup* per il **gruppo Host,** quindi selezionare *1* per **Dominio guasto**. Lasciare le impostazioni predefinite per il resto dei campi.
1. Al termine, selezionare **Rivedi : creare** e attendere la convalida.
1. Dopo aver visualizzato il messaggio **Convalida superata,** selezionare **Crea** per creare l'host.


