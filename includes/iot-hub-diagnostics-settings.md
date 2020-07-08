---
title: includere il file
description: includere file
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 02/20/2019
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: f3cab4909937bbf5ccb0f72b194b08810c0487c2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84792022"
---
### <a name="enable-logging-with-diagnostics-settings"></a>Abilitare la registrazione con le impostazioni di diagnostica

[!INCLUDE [updated-for-az](./updated-for-az.md)]

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

   * Connessioni
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
   * Traccia distribuita (anteprima)
   * Configurazioni
   * Flussi del dispositivo
   * Metriche del dispositivo

6. Salvare le nuove impostazioni. 

Per attivare le impostazioni di diagnostica con PowerShell, usare il codice seguente:

```azurepowershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <subscription that includes your IoT Hub>
Set-AzDiagnosticSetting -ResourceId <your resource Id> -ServiceBusRuleId <your service bus rule Id> -Enabled $true
```

Le nuove impostazioni diventano effettive in circa 10 minuti. Trascorso questo tempo, i log vengono visualizzati nella destinazione di archiviazione configurata, nel pannello **Impostazioni di diagnostica**. Per altre informazioni sulla configurazione della diagnostica, vedere [raccogliere e utilizzare i dati di log dalle risorse di Azure](../articles/azure-monitor/platform/platform-logs-overview.md).
