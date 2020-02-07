---
title: Trasferire le sottoscrizioni di Azure tra sottoscrittori e CSP
description: Informazioni su come trasferire le sottoscrizioni di Azure tra sottoscrittori e CSP.
services: billing
author: bandersmsft
ms.reviewer: dhgandhi
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: banders
ms.openlocfilehash: 08aeac9a8cd21e6f13bab0ee10ba4d212d96c5b0
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76775415"
---
# <a name="transfer-azure-subscriptions-between-subscribers-and-csps"></a>Trasferire le sottoscrizioni di Azure tra sottoscrittori e CSP

Questo articolo illustra i passaggi di alto livello usati per trasferire le sottoscrizioni di Azure tra i partner di Cloud Solution Provider (CSP) e i clienti.

## <a name="transfer-ea-subscriptions"></a>Traferire le sottoscrizioni EA

I partner con fatturazione diretta CSP certificati come [Azure Expert Managed Services Provider (MSP)](https://partner.microsoft.com/membership/azure-expert-msp) possono richiedere di trasferire le sottoscrizioni di Azure per i clienti che dispongono di un Contratto Enterprise diretto (EA). I trasferimenti di sottoscrizioni sono consentiti solo per i clienti che hanno accettato un Contratto del cliente Microsoft e hanno acquistato un piano Azure.

Quando la richiesta viene approvata, il CSP può fornire una fattura combinata ai clienti. Per altre informazioni sui DSN che trasferiscono le sottoscrizioni, vedere [Ottenere la proprietà della fatturazione delle sottoscrizioni di Azure per l'account MPA](mpa-request-ownership.md).

## <a name="other-subscription-transfers-to-a-csp-partner"></a>Altri trasferimenti di sottoscrizione a un partner CSP

Per trasferire altre sottoscrizioni di Azure a un partner CSP, il sottoscrittore deve spostare le risorse dalle sottoscrizioni di origine alle sottoscrizioni CSP. Usare il materiale sussidiario seguente per spostare le risorse tra le sottoscrizioni.

1. Collaborare con il partner CSP per creare sottoscrizioni di Azure CSP di destinazione.
1. Verificare che le sottoscrizioni CSP di origine e di destinazione si trovino nello stesso tenant di Azure Active Directory (Azure AD).  
    Non è possibile modificare il tenant di Azure AD per una sottoscrizione Azure CSP. Al contrario, è necessario aggiungere o associare la sottoscrizione di origine al tenant di Azure AD CSP. Per altre informazioni, vedere [Associare o aggiungere una sottoscrizione di Azure al tenant di Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).
    > [!IMPORTANT]
    > - Quando si associa una sottoscrizione a una directory Azure AD diversa, gli utenti che dispongono di ruoli assegnati che usano il [controllo degli accessi in base al ruolo (RBAC)](../../role-based-access-control/role-assignments-portal.md) perdono l'accesso. Anche gli amministratori delle sottoscrizioni classiche, tra cui l'amministratore del servizio e i coamministratori, perdono l'accesso. 
    > - Anche le assegnazioni dei criteri vengono rimosse da una sottoscrizione quando la sottoscrizione è associata a una directory diversa.
1. L'account utente utilizzato per il trasferimento deve avere [ il controllo degli accessi in base al ruolo](add-change-subscription-administrator.md) proprietario per entrambe le sottoscrizioni.
1. Prima di iniziare, [convalidare](/rest/api/resources/resources/validatemoveresources) l'opzione secondo cui tutte le risorse di Azure possono essere spostate dalla sottoscrizione di origine alla sottoscrizione di destinazione.  
    Alcune risorse di Azure non possono essere spostate tra le sottoscrizioni. Per visualizzare l'elenco completo di risorse di Azure che possono essere spostate, vedere [Supporto per lo spostamento delle risorse](../../azure-resource-manager/management/move-support-resources.md).
    > [!IMPORTANT]
    >  - Azure CSP supporta solo risorse di Azure Resource Manager. Se una risorsa di Azure della sottoscrizione di origine è stata creata con il modello di distribuzione classica di Azure, prima della migrazione è necessario spostarla ad [Azure Resource Manager](https://docs.microsoft.com/azure/cloud-solution-provider/migration/ea-payg-to-azure-csp/ea-open-direct-asm-to-arm). È necessario essere un partner per visualizzare la pagina Web.

1. Verificare che tutti i servizi di sottoscrizione di origine usino il modello di Azure Resource Manager. Trasferire quindi le risorse dalla sottoscrizione di origine alla sottoscrizione di destinazione usando [Azure Resource Move](../../azure-resource-manager/management/move-resource-group-and-subscription.md).
    > [!IMPORTANT]
    >  - Il trasferimento di risorse di Azure tra sottoscrizioni può causare tempi di inattività del servizio, in base alle risorse presenti nelle sottoscrizioni.

## <a name="all-subscription-transfers-from-a-csp-partner"></a>Tutti i trasferimenti di sottoscrizione da un partner CSP

Per trasferire altre sottoscrizioni da un partner CSP a qualunque altra offerta di Azure, il sottoscrittore deve spostare le risorse tra le sottoscrizioni CSP di origine e le sottoscrizioni CSP di destinazione.

1. Creare sottoscrizioni di Azure di destinazione.
1. Verificare che le sottoscrizioni di origine e di destinazione si trovino nello stesso tenant di Azure Active Directory (Azure AD). Per altre informazioni sulla modifica di un tenant Azure AD, vedere [Associare o aggiungere una sottoscrizione di Azure al tenant di Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

    > [!IMPORTANT]
    >  - Quando si associa una sottoscrizione a una directory diversa, gli utenti che dispongono di ruoli assegnati che usano il [controllo degli accessi in base al ruolo](../../role-based-access-control/role-assignments-portal.md) perdono l'accesso. Anche gli amministratori delle sottoscrizioni classiche, tra cui l'amministratore del servizio e i coamministratori, perdono l'accesso.
    >  - Anche le assegnazioni dei criteri vengono rimosse da una sottoscrizione quando la sottoscrizione è associata a una directory diversa.

1. L'account utente utilizzato per il trasferimento deve avere [ il controllo degli accessi in base al ruolo](add-change-subscription-administrator.md) proprietario per entrambe le sottoscrizioni.
1. Prima di iniziare, [convalidare](/rest/api/resources/resources/validatemoveresources) l'opzione secondo cui tutte le risorse di Azure possono essere spostate dalla sottoscrizione di origine alla sottoscrizione di destinazione.
    > [!IMPORTANT]
    >  - Alcune risorse di Azure non possono essere spostate tra le sottoscrizioni. Per visualizzare l'elenco completo di risorse di Azure che possono essere spostate, vedere [Supporto per lo spostamento delle risorse](../../azure-resource-manager/management/move-support-resources.md).

1. Trasferire le risorse dalla sottoscrizione di origine alla sottoscrizione di destinazione usando [Azure Resource Move](../../azure-resource-manager/management/move-resource-group-and-subscription.md).
    > [!IMPORTANT]
    >  - Il trasferimento di risorse di Azure tra sottoscrizioni può causare tempi di inattività del servizio, in base alle risorse presenti nelle sottoscrizioni.

## <a name="next-steps"></a>Passaggi successivi
- [Ottenere la proprietà della fatturazione delle sottoscrizioni di Azure per l'account del Contratto Microsoft Partner](mpa-request-ownership.md).
- Leggere le informazioni su come [Gestire account e sottoscrizioni con la fatturazione di Azure](index.yml).
