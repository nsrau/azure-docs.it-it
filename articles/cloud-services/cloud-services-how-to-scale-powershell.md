---
title: Aumentare o ridurre il numero di istanze per un servizio cloud Azure in Windows PowerShell | Documentazione Microsoft
description: (classico) Informazioni su come usare PowerShell per aumentare o ridurre il numero di istanze per un ruolo Web o un ruolo di lavoro in Azure.
services: cloud-services
documentationcenter: 
author: seanmck
manager: timlt
editor: 
ms.assetid: ee37dd8c-6714-4c61-adb8-03d6bbf76c9a
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2016
ms.author: seanmck
translationtype: Human Translation
ms.sourcegitcommit: 4acea8e7f4714754e6b3e54e9aaa622219ca9f7f
ms.openlocfilehash: f16d981a0dc33295c1981d9c282beb1d3243d9ef


---
# <a name="how-to-scale-a-cloud-service-in-powershell"></a>Come aumentare o ridurre il numero di istanze per un servizio cloud in PowerShell

È possibile usare Windows PowerShell per aumentare o ridurre il numero di istanze per un ruolo Web o un ruolo di lavoro.  

## <a name="log-in-to-azure"></a>Accedere ad Azure

Prima di poter eseguire qualsiasi operazione sulla sottoscrizione tramite PowerShell, è necessario eseguire l'accesso:

```powershell
Add-AzureAccount
```

Se sono disponibili più sottoscrizioni associate all'account, potrebbe essere necessario modificare la sottoscrizione corrente a seconda di dove si trova il servizio cloud. Per controllare la sottoscrizione corrente, eseguire:

```powershell
Get-AzureSubscription -Current
```

Se è necessario modificare la sottoscrizione corrente, eseguire:

```powershell
Set-AzureSubscription -SubscriptionId <subscription_id>
```

## <a name="check-the-current-instance-count-for-your-role"></a>Controllare il numero corrente di istanze per il ruolo

Per controllare lo stato corrente del ruolo, eseguire:

```powershell
Get-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>'
```

Si dovrebbero così ottenere informazioni sul ruolo, inclusa la versione corrente del sistema operativo e il numero corrente di istanze. In questo caso, il ruolo ha una singola istanza.

![Informazioni sul ruolo](./media/cloud-services-how-to-scale-powershell/get-azure-role.png)

## <a name="scale-out-the-role-by-adding-more-instances"></a>Aumentare il numero di istanze per il ruolo aggiungendo istanze

Per aumentare il numero di istanze del ruolo, passare il numero desiderato con il parametro **Count** del cmdlet **Set-AzureRole**:

```powershell
Set-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>' -Slot <target_slot> -Count <desired_instances>
```

Il cmdlet si blocca temporaneamente durante il provisioning e l'avvio delle nuove istanze. Se durante questo periodo si apre una nuova finestra di PowerShell e si chiama **Get-AzureRole** come indicato in precedenza, verrà visualizzato il nuovo numero di istanze di destinazione. Se si controlla lo stato del ruolo nel portale, si vedrà che per la nuova istanza è indicato che l'avvio è in corso:

![Avvio dell'istanza VM in corso nel portale](./media/cloud-services-how-to-scale-powershell/role-instance-starting.png)

Una volta avviate le nuove istanze, il cmdlet restituirà il controllo indicando la riuscita dell'operazione:

![Aumento delle istanze del ruolo riuscito](./media/cloud-services-how-to-scale-powershell/set-azure-role-success.png)

## <a name="scale-in-the-role-by-removing-instances"></a>Ridurre il numero di istanze per il ruolo rimuovendo istanze

In modo analogo, è possibile ridurre il numero di istanze per un ruolo rimuovendo istanze. Impostare il parametro **Count** di **Set-AzureRole** sul numero di istanze da ottenere al termine dell'operazione.

## <a name="next-steps"></a>Passaggi successivi

Non è possibile configurare la scalabilità automatica per i servizi cloud da PowerShell. Per eseguire questa operazione, vedere [Come configurare la scalabilità automatica di un servizio cloud](cloud-services-how-to-scale-portal.md).



<!--HONumber=Dec16_HO1-->


