---
title: Come distribuire le dipendenze del cloud OPC gemelli in Azure | Microsoft Docs
description: Come distribuire le dipendenze di Azure Twin di Azure.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: cb07899b51280cff8613d637640c0da23debbc8e
ms.sourcegitcommit: df7942ba1f28903ff7bef640ecef894e95f7f335
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2019
ms.locfileid: "69016524"
---
# <a name="deploying-dependencies-for-local-development"></a>Distribuzione delle dipendenze per lo sviluppo locale

Questo articolo illustra come distribuire solo i servizi della piattaforma Azure necessari per lo sviluppo e il debug locali.   Al termine, sarà disponibile un gruppo di risorse che contiene tutto il necessario per lo sviluppo e il debug locale.

## <a name="deploy-azure-platform-services"></a>Distribuire i servizi della piattaforma Azure

1. Verificare che siano installate le estensioni [PowerShell e AzureRM PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) .  Aprire un prompt dei comandi o un terminale ed eseguire:

   ```bash
   git clone https://github.com/Azure/azure-iiot-components
   cd azure-iiot-components
   ```

   ```bash
   deploy -type local
   ```

2. Seguire le istruzioni per assegnare un nome al gruppo di risorse per la distribuzione.  Lo script distribuisce solo le dipendenze al gruppo di risorse nella sottoscrizione di Azure, ma non ai micro servizi.  Lo script registra inoltre un'applicazione in Azure Active Directory.  Questa operazione è necessaria per supportare l'autenticazione basata su OAUTH.  Questa operazione può richiedere alcuni minuti.

3. Una volta completato lo script, è possibile scegliere di salvare il file. env.  Il file dell'ambiente. env è il file di configurazione di tutti i servizi e gli strumenti che si desidera eseguire nel computer di sviluppo.  

## <a name="troubleshooting-deployment-failures"></a>Risoluzione degli errori di distribuzione

### <a name="resource-group-name"></a>Nome del gruppo di risorse

Assicurarsi di usare un nome di gruppo di risorse breve e semplice.  Il nome viene usato anche per assegnare un nome alle risorse, in quanto deve essere conforme ai requisiti di denominazione delle risorse.  

### <a name="azure-active-directory-aad-registration"></a>Registrazione di Azure Active Directory (AAD)

Lo script di distribuzione tenta di registrare le applicazioni AAD in Azure Active Directory.  A seconda dei diritti per il tenant AAD selezionato, l'operazione potrebbe non riuscire.   Sono disponibili tre opzioni:

1. Se si sceglie un tenant AAD da un elenco di tenant, riavviare lo script e sceglierne uno diverso dall'elenco.
2. In alternativa, distribuire un tenant AAD privato, riavviare lo script e scegliere di usarlo.
3. Continua senza autenticazione.  Poiché i micro servizi vengono eseguiti localmente, questo è accettabile, ma non simula gli ambienti di produzione.  

## <a name="next-steps"></a>Passaggi successivi

Ora che sono stati distribuiti i servizi OPC gemelli a un progetto esistente, ecco il passaggio successivo suggerito:

> [!div class="nextstepaction"]
> [Informazioni su come distribuire moduli gemelli OPC](howto-opc-twin-deploy-modules.md)
