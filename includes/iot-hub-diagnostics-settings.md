---
title: File di inclusione
description: File di inclusione
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 05/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: e518263fc7f427ade4943f91fdc840b62a235205
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2018
ms.locfileid: "53550024"
---
### <a name="enable-logging-with-diagnostics-settings"></a>Abilitare la registrazione con le impostazioni di diagnostica

1. Accedere al [portale di Azure](https://portal.azure.com) e passare all'hub IoT.

2. Selezionare **Impostazioni di diagnostica**.

3. Selezionare **Attiva diagnostica**.

   ![Attivare la diagnostica](./media/iot-hub-diagnostics-settings/turnondiagnostics.png)

4. Assegnare un nome alle impostazioni di diagnostica.

5. Scegliere dove si vogliono inviare i log. È possibile selezionare qualsiasi combinazione delle tre opzioni:

   * Archivia in un account di archiviazione
   * Streaming in un hub eventi
   * Invia a Log Analytics

6. Scegliere le operazioni che si vuole monitorare e abilitare i log per tali operazioni. Le operazioni per cui le impostazioni di diagnostica possono inviare report sono:

   * connessioni
   * Telemetria dei dispositivi
   * Messaggi da cloud a dispositivo
   * Operazioni relative alle identità dei dispositivi
   * Caricamenti di file
   * Routing dei messaggi
   * Operazioni da cloud a dispositivi gemelli
   * Operazioni da dispositivo gemello a cloud
   * Operazioni di dispositivo gemello
   * Operazioni di processo
   * Metodi diretti  

6. Salvare le nuove impostazioni. 

Per attivare le impostazioni di diagnostica con PowerShell, usare il codice seguente:

```azurepowershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName <subscription that includes your IoT Hub>
Set-AzureRmDiagnosticSetting -ResourceId <your resource Id> -ServiceBusRuleId <your service bus rule Id> -Enabled $true
```

Le nuove impostazioni diventano effettive in circa 10 minuti. Trascorso questo tempo, i log vengono visualizzati nella destinazione di archiviazione configurata, nel pannello **Impostazioni di diagnostica**. Per altre informazioni sulla configurazione della diagnostica, vedere [Raccogliere e usare i dati dei log dalle risorse di Azure](../articles/azure-monitor/platform/diagnostic-logs-overview.md).