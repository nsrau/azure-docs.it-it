---
title: Fasi della distribuzione di un progetto
description: Informazioni sui passaggi correlati alla sicurezza e agli elementi eseguiti dai servizi Blueprint di Azure durante la creazione di un'assegnazione di blueprint.
ms.date: 11/13/2019
ms.topic: conceptual
ms.openlocfilehash: 61d19c84cd659b9df3a272c5c2743944e51df06e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80677322"
---
# <a name="stages-of-a-blueprint-deployment"></a>Fasi della distribuzione di un progetto

Quando un blueprint viene distribuito, il servizio Blueprint di Azure esegue una serie di azioni per distribuire le risorse definite nel blueprint. In questo articolo vengono fornite informazioni dettagliate sulle operazioni di ogni passaggio.

La distribuzione del blueprint viene attivata assegnando un blueprint a una sottoscrizione o [aggiornando un'assegnazione esistente.](../how-to/update-existing-assignments.md) Durante la distribuzione, Blueprint di Azure esegue i passaggi di alto livello seguenti:During the deployment, Azure Blueprints takes the following high-level steps:

> [!div class="checklist"]
> - Azure Blueprints ha concesso diritti di proprietario
> - Viene creato l'oggetto assegnazione blueprint
> - Facoltativo- Azure Blueprints crea un'identità **gestita assegnata dal sistemaOptional** - Azure Blueprints creates system-assigned managed identity
> - L'identità gestita distribuisce gli elementi del blueprint
> - Il servizio Azure Blueprints e i diritti di identità gestita assegnati al sistema vengono revocatiAzure Blueprints service and **system-assigned** managed identity rights are revoked

## <a name="azure-blueprints-granted-owner-rights"></a>Azure Blueprints ha concesso diritti di proprietario

All'entità servizio Azure Blueprints vengono concessi diritti di proprietario per la sottoscrizione o le sottoscrizioni assegnate quando viene usata un'identità gestita [con identità gestita assegnata dal sistema.](../../../active-directory/managed-identities-azure-resources/overview.md) Il ruolo concesso consente a Azure Blueprints di creare e successivamente revocare l'identità gestita **assegnata dal sistema.** Se si usa un'identità gestita **assegnata dall'utente,** l'entità servizio Azure Blueprints non ottiene e non necessita dei diritti di proprietario per la sottoscrizione.

I diritti vengono concessi automaticamente se l'assegnazione viene eseguita tramite il portale. Tuttavia, se l'assegnazione viene eseguita tramite l'API REST, la concessione dei diritti deve essere eseguita con una chiamata API separata. L'AppId blueprint `f71766dc-90d9-4b7d-bd9d-4499c4331c3f`di Azure è , ma l'entità servizio varia in base al tenant. Usare [l'API Graph](../../../active-directory/develop/active-directory-graph-api.md) di Azure Active Directory e il servizio endpoint [RESTPrincipals](/graph/api/resources/serviceprincipal) per ottenere l'entità servizio. Concedere quindi al ruolo Azure Blueprints il ruolo _Owner_ tramite il modello [Portal](../../../role-based-access-control/role-assignments-portal.md), [Azure CLI](../../../role-based-access-control/role-assignments-cli.md), [Azure PowerShell](../../../role-based-access-control/role-assignments-powershell.md), REST [API](../../../role-based-access-control/role-assignments-rest.md)o [Resource Manager](../../../role-based-access-control/role-assignments-template.md).

Il servizio Blueprint di Azure non distribuisce direttamente le risorse.

## <a name="the-blueprint-assignment-object-is-created"></a>Viene creato l'oggetto assegnazione blueprint

Un utente, un gruppo o un'entità servizio assegna un blueprint a una sottoscrizione. L'oggetto assegnazione esiste a livello di sottoscrizione a cui è stato assegnato il blueprint. Le risorse create dalla distribuzione non vengono eseguite nel contesto dell'entità di distribuzione.

Durante la creazione dell'assegnazione del blueprint, viene selezionato il tipo di [identità gestita.](../../../active-directory/managed-identities-azure-resources/overview.md) L'impostazione predefinita è un'identità **gestita assegnata dal sistema.** È possibile scegliere un'identità gestita **assegnata dall'utente.** Quando si utilizza un'identità gestita **assegnata dall'utente,** è necessario definire e concedere le autorizzazioni prima della creazione dell'assegnazione del blueprint. Entrambi i ruoli predefiniti [Proprietario](../../../role-based-access-control/built-in-roles.md#owner) e [Operatore blueprint](../../../role-based-access-control/built-in-roles.md#blueprint-operator) dispongono dell'autorizzazione necessaria `blueprintAssignment/write` per creare un'assegnazione che utilizza un'identità gestita assegnata **dall'utente.**

## <a name="optional---azure-blueprints-creates-system-assigned-managed-identity"></a>Facoltativo- Azure Blueprints crea un'identità gestita assegnata dal sistemaOptional - Azure Blueprints creates system-assigned managed identity

Quando durante l'assegnazione viene selezionata [l'identità gestita assegnata dal sistema,](../../../active-directory/managed-identities-azure-resources/overview.md) Azure Blueprints crea l'identità e concede all'identità gestita il ruolo [di proprietario.](../../../role-based-access-control/built-in-roles.md#owner) Se [un'assegnazione esistente viene aggiornata,](../how-to/update-existing-assignments.md)Azure Blueprints usa l'identità gestita creata in precedenza.

L'identità gestita correlata all'assegnazione del blueprint viene utilizzata per distribuire o ridistribuire le risorse definite nel blueprint. Questa progettazione evita che le assegnazioni interferiscano inavvertitamente tra loro.
Questa progettazione supporta anche la funzionalità di blocco delle [risorse](./resource-locking.md) controllando la sicurezza di ogni risorsa distribuita dal blueprint.

## <a name="the-managed-identity-deploys-blueprint-artifacts"></a>L'identità gestita distribuisce gli elementi del blueprint

L'identità gestita attiva quindi le distribuzioni di Resource Manager degli elementi all'interno del blueprint [nell'ordine di sequenza](./sequencing-order.md)definito. L'ordine può essere modificato per garantire che gli elementi dipendenti da altri elementi vengano distribuiti nell'ordine corretto.

Un errore di accesso da parte di una distribuzione è spesso il risultato del livello di accesso concesso all'identità gestita. Il servizio Blueprint di Azure gestisce il ciclo di vita della sicurezza dell'identità **gestita assegnata dal sistema.** Tuttavia, l'utente è responsabile della gestione dei diritti e del ciclo di vita di un'identità gestita **assegnata dall'utente.**

## <a name="blueprint-service-and-system-assigned-managed-identity-rights-are-revoked"></a>Il servizio blueprint e i diritti di identità gestita assegnati dal sistema vengono revocati

Una volta completate le distribuzioni, Azure Blueprints revoca i diritti dell'identità gestita assegnata dal **sistema** dalla sottoscrizione. Quindi, il servizio Blueprint di Azure revoca i diritti dalla sottoscrizione. La rimozione dei diritti impedisce a Azure Blueprints di diventare un proprietario permanente in una sottoscrizione.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come usare [parametri statici e dinamici](parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](resource-locking.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../how-to/update-existing-assignments.md).
- Risolvere i problemi durante l'assegnazione di un progetto con la [risoluzione generale dei problemi](../troubleshoot/general.md).
