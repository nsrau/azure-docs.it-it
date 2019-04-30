---
author: rockboyfor
ms.service: virtual-machines
ms.topic: include
origin.date: 10/26/2018
ms.date: 11/26/2018
ms.author: v-yeche
ms.openlocfilehash: 8ae22ec7f75b9cd0d7958977dfd97169706c9389
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097667"
---
Dopo aver creato il listener del gruppo di disponibilità, può essere necessario regolare i parametri cluster RegisterAllProvidersIP e HostRecordTTL per la risorsa listener. Questi parametri possono ridurre il tempo di riconnessione dopo un failover che può impedire i timeout di connessione. Per altre informazioni su questi parametri e sull'esempio di codice, vedere [Creare o configurare un listener del gruppo di disponibilità](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover).

<!-- Update_Description: update meta properties -->