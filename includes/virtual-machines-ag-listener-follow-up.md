---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 8ae22ec7f75b9cd0d7958977dfd97169706c9389
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67180094"
---
Dopo aver creato il listener del gruppo di disponibilità, può essere necessario regolare i parametri cluster RegisterAllProvidersIP e HostRecordTTL per la risorsa listener. Questi parametri possono ridurre il tempo di riconnessione dopo un failover che può impedire i timeout di connessione. Per altre informazioni su questi parametri e sull'esempio di codice, vedere [Creare o configurare un listener del gruppo di disponibilità](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover).

