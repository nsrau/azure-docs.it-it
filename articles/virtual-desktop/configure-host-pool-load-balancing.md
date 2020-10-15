---
title: Configurare il bilanciamento del carico per desktop virtuali Windows-Azure
description: Come configurare il metodo di bilanciamento del carico per un ambiente desktop virtuale di Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 10/12/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 2c57ac10fbd318dd4bbb2dc86457e186dd824834
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/12/2020
ms.locfileid: "91951656"
---
# <a name="configure-the-windows-virtual-desktop-load-balancing-method"></a>Configurare il metodo di bilanciamento del carico di Desktop virtuale Windows

La configurazione del metodo di bilanciamento del carico per un pool host consente di modificare l'ambiente di desktop virtuale Windows in base alle proprie esigenze.

>[!NOTE]
> Questa operazione non si applica a un pool host del desktop persistente perché gli utenti hanno sempre un mapping 1:1 a un host sessione nel pool host.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che siano state seguite le istruzioni riportate in [configurare il modulo PowerShell per desktop virtuale Windows](powershell-module.md) per scaricare e installare il modulo PowerShell e accedere all'account Azure.

## <a name="configure-breadth-first-load-balancing"></a>Configurare la larghezza-primo bilanciamento del carico

Il bilanciamento del carico con un primo respiro è la configurazione predefinita per i nuovi pool host non permanenti. Il bilanciamento del carico per la prima volta distribuisce le nuove sessioni utente in tutti gli host di sessione disponibili nel pool di host. Quando si configura il bilanciamento del carico per il primo livello, è possibile impostare un limite massimo di sessioni per ogni host sessione nel pool host.

Per configurare un pool di host per eseguire il bilanciamento del carico con ampiezza del primo senza modificare il limite massimo della sessione, eseguire il cmdlet PowerShell seguente:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'BreadthFirst'
```

Successivamente, per assicurarsi di aver impostato il metodo di bilanciamento del carico per la prima volta, eseguire il cmdlet seguente:

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, LoadBalancerType

Name             : hostpoolname
LoadBalancerType : BreadthFirst
```

Per configurare un pool di host per eseguire il bilanciamento del carico con ampiezza e per l'uso di un nuovo limite massimo di sessioni, eseguire il cmdlet PowerShell seguente:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'BreadthFirst' -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>Configurare la profondità-primo bilanciamento del carico

Il bilanciamento del carico depth-first distribuisce le nuove sessioni utente a un host di sessione disponibile con il numero massimo di connessioni, ma non ha raggiunto la soglia massima di limite della sessione.

>[!IMPORTANT]
>Quando si configura il bilanciamento del carico per il primo livello di profondità, è necessario impostare un limite massimo di sessioni per host sessione nel pool host.

Per configurare un pool di host per eseguire il bilanciamento del carico per il primo livello di profondità, eseguire il cmdlet PowerShell seguente:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'DepthFirst' -MaxSessionLimit ###
```

>[!NOTE]
> L'algoritmo di bilanciamento del carico depth-first distribuisce le sessioni agli host di sessione in base al limite massimo di host di sessione ( `-MaxSessionLimit` ). Il valore predefinito di questo parametro è `999999` , che rappresenta anche il numero massimo possibile a cui è possibile impostare questa variabile. Questo parametro è obbligatorio quando si usa l'algoritmo di bilanciamento del carico con il primo livello di profondità. Per un'esperienza utente ottimale, assicurarsi di modificare il parametro limite massimo host sessione in un numero più adatto all'ambiente in uso.

Per assicurarsi che l'impostazione sia stata aggiornata, eseguire il cmdlet seguente:

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, LoadBalancerType, MaxSessionLimit

Name             : hostpoolname
LoadBalancerType : DepthFirst
MaxSessionLimit  : 6
```

## <a name="configure-load-balancing-with-the-azure-portal"></a>Configurare il bilanciamento del carico con il portale di Azure

È anche possibile configurare il bilanciamento del carico con il portale di Azure.

Per configurare il bilanciamento del carico:

1. Accedere al portale di Azure all'indirizzo https://portal.azure.com.
2. Cercare e selezionare **desktop virtuale Windows** in servizi.
3. Nella pagina desktop virtuale di Windows selezionare **pool host**.
4. Selezionare il nome del pool host che si desidera modificare.
5. Selezionare **Proprietà**.
6. Immettere il **limite massimo di sessioni** nel campo e selezionare l' **algoritmo di bilanciamento del carico** desiderato per il pool host nel menu a discesa.
7. Selezionare **Salva**. Questa operazione applica le nuove impostazioni di bilanciamento del carico.