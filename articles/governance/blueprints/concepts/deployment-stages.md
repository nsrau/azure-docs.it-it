---
title: Fasi della distribuzione di un progetto
description: Informazioni sulla procedura i servizi di Azure Blueprint passa attraverso durante una distribuzione.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: d7000813b51fb9c9aae9a21cbded3ae0028e83f4
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59261418"
---
# <a name="stages-of-a-blueprint-deployment"></a>Fasi della distribuzione di un progetto

Quando viene distribuito un progetto, viene eseguita una serie di azioni dal servizio di progetti di architetture di Azure per distribuire le risorse definite nel progetto. Questo articolo fornisce informazioni dettagliate su cosa comporta ogni passaggio.

Linee guida per la distribuzione viene attivata tramite l'assegnazione di un progetto a una sottoscrizione o [l'aggiornamento di un'assegnazione esistente](../how-to/update-existing-assignments.md). Durante la distribuzione, i progetti accetta i passaggi generali seguenti:

> [!div class="checklist"]
> - Progetti di architetture concessi i diritti di proprietario
> - Viene creato l'oggetto di assegnazione progetto
> - Crea facoltativo - progetti di architetture **assegnato dal sistema** identità gestita
> - L'identità gestita consente di distribuire elementi del progetto
> - Servizio del progetto e **assegnato dal sistema** identità gestita diritti vengono revocati

## <a name="blueprints-granted-owner-rights"></a>Progetti di architetture concessi i diritti di proprietario

L'entità servizio di Azure i progetti verrà concessi i diritti di proprietario per la sottoscrizione assegnato o le sottoscrizioni. Il ruolo concesso consente linee guida per creare e in un secondo momento si revoca, il [identità assegnata dal sistema gestito](../../../active-directory/managed-identities-azure-resources/overview.md).

I diritti vengono concessi automaticamente se viene eseguita l'assegnazione tramite il portale. Tuttavia, se viene eseguita l'assegnazione tramite l'API REST, concedere i diritti deve essere con un'API distinta chiamare. Azure Blueprint AppId è `f71766dc-90d9-4b7d-bd9d-4499c4331c3f`, ma l'entità servizio varia in base al tenant. Uso [API di Graph di Azure Active Directory](../../../active-directory/develop/active-directory-graph-api.md) e l'endpoint REST [servicePrincipals](/graph/api/resources/serviceprincipal) per ottenere l'entità servizio. Quindi concedere le linee guida di Azure i _proprietario_ ruolo tramite il [portale](../../../role-based-access-control/role-assignments-portal.md), [Azure CLI](../../../role-based-access-control/role-assignments-cli.md), [Azure PowerShell](../../../role-based-access-control/role-assignments-powershell.md), [REST API](../../../role-based-access-control/role-assignments-rest.md), o un [modello di Resource Manager](../../../role-based-access-control/role-assignments-template.md).

Il servizio di progetti non distribuisce direttamente le risorse.

## <a name="the-blueprint-assignment-object-is-created"></a>Viene creato l'oggetto di assegnazione progetto

Un utente, gruppo o entità servizio assegna un progetto a una sottoscrizione. L'oggetto di assegnazione esistente a livello di sottoscrizione in cui è stato assegnato il progetto. Le risorse create dalla distribuzione non vengono eseguite nel contesto dell'entità di distribuzione.

Durante la creazione dell'assegnazione progetto, il tipo di [identità gestita](../../../active-directory/managed-identities-azure-resources/overview.md) sia selezionata. Il valore predefinito è una **assegnato dal sistema** identità gestita. Oggetto **assegnata dall'utente** identità gestita è possibile scegliere. Quando si usa un' **assegnata dall'utente** managed identity, deve essere definito e le autorizzazioni concesse, prima che venga creato l'assegnazione di progetto.

## <a name="optional---blueprints-creates-system-assigned-managed-identity"></a>Facoltativo: linee guida per Crea assegnato dal sistema di identità gestita

Quando [identità assegnata dal sistema gestito](../../../active-directory/managed-identities-azure-resources/overview.md) sia selezionata durante l'assegnazione, crea l'identità di progetti e concede l'identità gestita la [proprietario](../../../role-based-access-control/built-in-roles.md#owner) ruolo. Se un' [assegnazione esistente viene aggiornata](../how-to/update-existing-assignments.md), linee guida Usa l'identità gestita creata in precedenza.

L'identità gestita correlato per l'assegnazione di progetto viene utilizzato per distribuire o ridistribuire le risorse definite nel progetto. Questa progettazione consente di evitare le assegnazioni inavvertitamente interferiscono tra loro.
Questa struttura supporta anche il [blocco delle risorse](./resource-locking.md) funzionalità controllando la sicurezza di ogni risorsa distribuita dal progetto iniziale.

## <a name="the-managed-identity-deploys-blueprint-artifacts"></a>L'identità gestita consente di distribuire elementi del progetto

L'identità gestita attiva quindi le distribuzioni di Resource Manager gli elementi all'interno di progetto in definiti [ordine di sequenziazione](./sequencing-order.md). È possibile regolare l'ordine per assicurarsi che gli elementi dipendenti da altri elementi vengono distribuiti nell'ordine corretto.

Un errore di accesso da una distribuzione è spesso il risultato del livello di accesso concesso all'identità gestita. Il servizio di progetti di architetture gestisce il ciclo di vita di protezione del **assegnato dal sistema** identità gestita. Tuttavia, l'utente è responsabile della gestione dei diritti e un ciclo di vita di un **assegnata dall'utente** identità gestita.

## <a name="blueprint-service-and-system-assigned-managed-identity-rights-are-revoked"></a>Servizio di progetto e assegnato dal sistema di identità gestita diritti vengono revocati

Una volta completate le distribuzioni, i progetti di revocare i diritti del **assegnato dal sistema** identità gestita dalla sottoscrizione. Quindi, il servizio di progetti di architetture revoca i diritti della sottoscrizione. Rimozione di diritti impedisce progetti di architetture di diventare un proprietario permanente in una sottoscrizione.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come usare [parametri statici e dinamici](parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](resource-locking.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../how-to/update-existing-assignments.md).
- Risolvere i problemi durante l'assegnazione di un progetto con la [risoluzione generale dei problemi](../troubleshoot/general.md).