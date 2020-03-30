---
title: Come distribuire le dipendenze del cloud OPC Twin in Azure Documenti Microsoft
description: Questo articolo descrive come distribuire le dipendenze di OPC Twin Azure necessarie per eseguire lo sviluppo e il debug locali.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 231d1efa02ec80e8ad56a8895d4262d774480111
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73824110"
---
# <a name="deploying-dependencies-for-local-development"></a>Distribuzione delle dipendenze per lo sviluppo localeDeploying dependencies for local development

Questo articolo illustra come distribuire solo i servizi della piattaforma Azure necessari per eseguire lo sviluppo e il debug locali.   Alla fine, si dispererà un gruppo di risorse che contiene tutto il necessario per lo sviluppo locale e il debug.

## <a name="deploy-azure-platform-services"></a>Distribuire i servizi della piattaforma AzureDeploy Azure platform services

1. Assicurarsi di avere installato le estensioni di PowerShell e [AzureRM PowerShell.Make](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) sure that you have PowerShell and AzureRM PowerShell extensions installed.  Aprire un prompt dei comandi o un terminale ed eseguire:

   ```bash
   git clone https://github.com/Azure/azure-iiot-components
   cd azure-iiot-components
   ```

   ```bash
   deploy -type local
   ```

2. Seguire le istruzioni visualizzate per assegnare un nome al gruppo di risorse per la distribuzione.  Lo script distribuisce solo le dipendenze a questo gruppo di risorse nella sottoscrizione di Azure, ma non i microservizi.  Lo script registra anche un'applicazione in Azure Active Directory.The script also registers an Application in Azure Active Directory.  Ciò è necessario per supportare l'autenticazione basata su OAUTH.  Questa operazione può richiedere alcuni minuti.

3. Una volta completato lo script, è possibile scegliere di salvare il file .env.  Il file di ambiente .env è il file di configurazione di tutti i servizi e gli strumenti che si desidera eseguire nel computer di sviluppo.  

## <a name="troubleshooting-deployment-failures"></a>Risoluzione degli errori di distribuzioneTroubleshooting deployment failures

### <a name="resource-group-name"></a>Nome del gruppo di risorse

Assicurarsi di usare un nome di gruppo di risorse breve e semplice.  Il nome viene utilizzato anche per denominare le risorse in quanto tali deve essere conforme ai requisiti di denominazione delle risorse.  

### <a name="azure-active-directory-aad-registration"></a>Registrazione di Azure Active Directory (AAD)Azure Active Directory (AAD) registration

Lo script di distribuzione tenta di registrare le applicazioni AAD in Azure Active Directory.The deployment script tries to register AAD applications in Azure Active Directory.  A seconda dei diritti per il tenant AAD selezionato, l'operazione potrebbe non riuscire.   Sono disponibili tre opzioni:

1. Se si sceglie un tenant AAD da un elenco di tenant, riavviare lo script e sceglierne uno diverso dall'elenco.
2. In alternativa, distribuire un tenant AAD privato, riavviare lo script e scegliere di utilizzarlo.
3. Continuare senza autenticazione.  Poiché si eseguono i microservizi in locale, questo è accettabile, ma non imita gli ambienti di produzione.  

## <a name="next-steps"></a>Passaggi successivi

Ora che i servizi OPC Twin sono stati distribuiti correttamente in un progetto esistente, ecco il passaggio successivo suggerito:

> [!div class="nextstepaction"]
> [Scopri come distribuire i moduli OPC Twin](howto-opc-twin-deploy-modules.md)
