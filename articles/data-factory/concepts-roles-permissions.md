---
title: Ruoli e autorizzazioni per Azure Data Factory
description: Descrive i ruoli e le autorizzazioni richiesti per creare data factory e per lavorare con le risorse figlio.
ms.date: 11/5/2018
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
ms.tgt_pltfrm: na
author: djpmsft
ms.author: daperlov
manager: craigg
ms.openlocfilehash: f5b4cbc670a429a84f42be79adb00057656a5483
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681418"
---
# <a name="roles-and-permissions-for-azure-data-factory"></a>Ruoli e autorizzazioni per Azure Data Factory

Questo articolo descrive i ruoli necessari per creare e gestire le risorse di Azure Data Factory e le autorizzazioni concesse da tali ruoli.

## <a name="roles-and-requirements"></a>Ruoli e requisiti

Per creare istanze di Data Factory, l'account utente usato per accedere ad Azure deve essere un membro del ruolo *collaboratore* o *proprietario* oppure un *amministratore* della sottoscrizione di Azure. Per visualizzare le autorizzazioni disponibili nella sottoscrizione, nel portale di Azure selezionare il nome utente nell'angolo in alto a destra e quindi selezionare **Autorizzazioni**. Se si accede a più sottoscrizioni, selezionare quella appropriata. 

Per creare e gestire le risorse figlio per Data Factory, inclusi i set di dati, i servizi collegati, le pipeline, i trigger e i runtime di integrazione, sono applicabili i requisiti seguenti:
- Per creare e gestire le risorse figlio nel portale di Azure, è necessario appartenere al ruolo **Collaboratore Data Factory** a livello di gruppo di risorse o superiore.
- Per creare e gestire le risorse figlio con PowerShell o l'SDK, è sufficiente il ruolo di **collaboratore** a livello di risorsa o superiore.

Per istruzioni di esempio su come aggiungere un utente a un ruolo, vedere l'articolo [Aggiungere i ruoli](../billing/billing-add-change-azure-subscription-administrator.md).

## <a name="set-up-permissions"></a>Impostare le autorizzazioni

Dopo aver creato una data factory è possibile consentire ad altri utenti di lavorare con essa. Per concedere l'accesso ad altri utenti, è necessario aggiungerli al ruolo **Collaboratore Data factory** predefinito sul gruppo di risorse che contiene la data factory.

### <a name="scope-of-the-data-factory-contributor-role"></a>Ambito del ruolo Collaboratore Data factory

L'appartenenza al ruolo **Collaboratore Data factory** consente agli utenti di eseguire le operazioni seguenti:
- Creare, modificare ed eliminare le data factory e le risorse figlio, inclusi i set di dati, i servizi collegati, le pipeline, i trigger e i runtime di integrazione.
- Distribuire i modelli di Resource Manager. La distribuzione di Resource Manager è il metodo di distribuzione usato da Data Factory nel portale di Azure.
- Gestire gli avvisi di Application Insights per una data factory.
- Creare i ticket di supporto.

Per altre informazioni su questo ruolo, vedere il [ruolo Collaboratore Data factory](../role-based-access-control/built-in-roles.md#data-factory-contributor).

### <a name="resource-manager-template-deployment"></a>Distribuzione del modello di Resource Manager

Il ruolo **Collaboratore Data factory**, a livello di gruppo di risorse o superiore, consente agli utenti di distribuire i modelli di Resource Manager. Di conseguenza, i membri del ruolo possono usare i modelli di Resource Manager per distribuire le data factory e le relative risorse figlio, inclusi i set di dati, i servizi collegati, le pipeline, i trigger e i runtime di integrazione. L'appartenenza a questo ruolo non consente tuttavia all'utente di creare altre risorse.

Le autorizzazioni per Azure Repos e GitHub sono indipendenti dalle autorizzazioni di Data Factory. Di conseguenza, un utente con autorizzazioni di repository che è solo un membro del ruolo lettore può modificare le risorse figlio di Data Factory ed eseguire il commit delle modifiche nel repository, ma non può pubblicare tali modifiche.

> [!IMPORTANT]
> La distribuzione dei modelli di Resource Manager con il ruolo **Collaboratore Data factory** non eleva le autorizzazioni. Ad esempio, se si distribuisce un modello che crea una macchina virtuale di Azure e non si è autorizzati a creare macchine virtuali, la distribuzione ha esito negativo con un errore di autorizzazione.

### <a name="custom-scenarios-and-custom-roles"></a>Scenari personalizzati e ruoli personalizzati

In alcuni casi potrebbe essere necessario concedere livelli di accesso diversi a utenti di data factory diversi. Ad esempio:
- Potrebbe servire un gruppo in cui gli utenti dispongano solo di autorizzazioni su una data factory specifica.
- Oppure potrebbe essere necessario un gruppo in cui gli utenti possano monitorare solo una data factory (o più data factory) ma non possano modificarla.

È possibile ottenere questi scenari personalizzati creando ruoli personalizzati e assegnando gli utenti a tali ruoli. Per altre informazioni sui ruoli personalizzati, vedere [Ruoli personalizzati in Azure](..//role-based-access-control/custom-roles.md).

Ecco alcuni esempi che illustrano cosa si può ottenere con i ruoli personalizzati:

- Consentire a un utente di creare, modificare o eliminare qualsiasi data factory in un gruppo di risorse dal portale di Azure.

  Assegnare il ruolo **Collaboratore Data factory** a livello di gruppo di risorse per l'utente. Se si desidera consentire l'accesso a qualsiasi data factory in una sottoscrizione, assegnare il ruolo a livello di sottoscrizione.

- Consentire a un utente di visualizzare (leggere) e monitorare una data factory, ma non di modificarla o cambiarla.

  Assegnare il ruolo **lettore** predefinito alla risorsa di data factory per l'utente.

- Consentire a un utente di modificare una sola data factory nel portale di Azure.

  Questo scenario richiede due assegnazioni di ruolo.

  1. Assegnare il ruolo **collaboratore** predefinito a livello di data factory.
  2. Creare un ruolo personalizzato con l'autorizzazione **Microsoft.Resources/deployments/** . Assegnare questo ruolo personalizzato all'utente a livello di gruppo di risorse.

- Consentire a un utente di testare la connessione solo in un servizio collegato

    Creare un ruolo di ruolo personalizzato con le autorizzazioni per le azioni seguenti: **Microsoft. DataFactory/factorys/getFeatureValue/Read** e **Microsoft. DataFactory/factorys/getDataPlaneAccess/Read**. Assegnare questo ruolo personalizzato alla risorsa data factory per l'utente.

- Consentire a un utente di aggiornare una data factory da PowerShell o da SDK, ma non nel portale di Azure.

  Assegnare il ruolo **collaboratore** predefinito alla risorsa di data factory per l'utente. Questo ruolo consente all'utente di vedere le risorse nel portale di Azure, ma di non poter interagire con i pulsanti **Pubblica** e **Pubblica tutti**.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui ruoli in Azure - [Comprendere le definizioni dei ruoli](../role-based-access-control/role-definitions.md)

- Altre informazioni sul ruolo **Collaboratore Data factory** - [Ruolo Collaboratore Data factory](../role-based-access-control/built-in-roles.md#data-factory-contributor).
