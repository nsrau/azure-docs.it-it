---
title: Trasferire le sottoscrizioni di Azure tra sottoscrittori e CSP
description: Informazioni su come trasferire le sottoscrizioni di Azure tra sottoscrittori e CSP.
author: bandersmsft
ms.reviewer: dhgandhi
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 11/17/2020
ms.author: banders
ms.openlocfilehash: 7cbb628f6af286141fb1c7b330117ded33142c4f
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94744239"
---
# <a name="transfer-azure-subscriptions-between-subscribers-and-csps"></a>Trasferire le sottoscrizioni di Azure tra sottoscrittori e CSP

Questo articolo illustra i passaggi generali necessari per trasferire le sottoscrizioni di Azure tra i partner Cloud Solution Provider (CSP) e i clienti. Le informazioni qui contenute sono destinate al sottoscrittore di Azure per consentire il coordinamento con il partner. Le informazioni usate dai partner Microsoft per il processo di trasferimento sono descritte in [Informazioni su come trasferire sottoscrizioni di Azure di un cliente a un altro partner](/partner-center/switch-azure-subscriptions-to-a-different-partner).

Prima di avviare una richiesta di trasferimento, è necessario scaricare o esportare le informazioni relative ai costi e alla fatturazione da conservare. Le informazioni di fatturazione e le informazioni sull'utilizzo non vengono infatti trasferite con la sottoscrizione. Per altre informazioni sull'esportazione dei dati di gestione dei costi, vedere [Creare e gestire dati esportati](../costs/tutorial-export-acm-data.md). Per altre informazioni sul download delle fatture e dei dati sull'utilizzo, vedere [Scaricare o visualizzare la fattura e i dati di uso giornalieri di Azure](download-azure-invoice-daily-usage-date.md).

Se sono presenti delle prenotazioni, non vengono più applicate dopo il trasferimento della sottoscrizione. Assicurarsi quindi di [annullare le eventuali prenotazioni e di rimborsarle](../reservations/exchange-and-refund-azure-reservations.md) prima di trasferire una sottoscrizione.

## <a name="transfer-ea-subscriptions-to-a-csp-partner"></a>Trasferire sottoscrizioni con contratto Enterprise a un partner CSP

I partner con fatturazione diretta CSP certificati come [Azure Expert Managed Services Provider (MSP)](https://partner.microsoft.com/membership/azure-expert-msp) possono richiedere di trasferire le sottoscrizioni di Azure per i clienti che dispongono di un Contratto Enterprise diretto (EA). I trasferimenti di sottoscrizioni sono consentiti solo per i clienti che hanno accettato un Contratto del cliente Microsoft e hanno acquistato un piano di Azure con il programma CSP.

Quando la richiesta viene approvata, il CSP può fornire una fattura combinata ai clienti. Per altre informazioni sui DSN che trasferiscono le sottoscrizioni, vedere [Ottenere la proprietà della fatturazione delle sottoscrizioni di Azure per l'account MPA](mpa-request-ownership.md).

>[!IMPORTANT]
> Dopo il trasferimento di una sottoscrizione con Contratto Enterprise a un partner CSP, per eventuali incrementi della quota applicati in precedenza alla sottoscrizione con Contratto Enterprise verrà ripristinato il valore predefinito. Se sono necessarie quote aggiuntive dopo il trasferimento della sottoscrizione, richiedere al provider CSP di inviare una richiesta di [incremento della quota](../../azure-portal/supportability/regional-quota-requests.md). 

## <a name="other-subscription-transfers-to-a-csp-partner"></a>Altri trasferimenti di sottoscrizione a un partner CSP

Per trasferire altre sottoscrizioni di Azure a un partner CSP, il sottoscrittore deve spostare le risorse dalle sottoscrizioni di origine alle sottoscrizioni CSP. Usare il materiale sussidiario seguente per spostare le risorse tra le sottoscrizioni.

1. Collaborare con il partner CSP per creare sottoscrizioni di Azure CSP di destinazione.
1. Verificare che le sottoscrizioni CSP di origine e di destinazione si trovino nello stesso tenant di Azure Active Directory (Azure AD).  
    Non è possibile modificare il tenant di Azure AD per una sottoscrizione Azure CSP. Al contrario, è necessario aggiungere o associare la sottoscrizione di origine al tenant di Azure AD CSP. Per altre informazioni, vedere [Associare o aggiungere una sottoscrizione di Azure al tenant di Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).
    > [!IMPORTANT]
    > - Quando si associa una sottoscrizione a una directory di Azure AD diversa, gli utenti che dispongono di ruoli assegnati con il [Controllo degli accessi in base al ruolo di Azure](../../role-based-access-control/role-assignments-portal.md) perdono l'accesso. Anche gli amministratori delle sottoscrizioni classiche, tra cui l'amministratore del servizio e i coamministratori, perdono l'accesso.
    > - Anche le assegnazioni dei criteri vengono rimosse da una sottoscrizione quando la sottoscrizione è associata a una directory diversa.
1. L'account utente usato per il trasferimento deve disporre dell'accesso come proprietario di [Controllo degli accessi in base al ruolo di Azure](add-change-subscription-administrator.md) per entrambe le sottoscrizioni.
1. Prima di iniziare, [convalidare](/rest/api/resources/resources/validatemoveresources) l'opzione secondo cui tutte le risorse di Azure possono essere spostate dalla sottoscrizione di origine alla sottoscrizione di destinazione.  
    Alcune risorse di Azure non possono essere spostate tra le sottoscrizioni. Per visualizzare l'elenco completo di risorse di Azure che possono essere spostate, vedere [Supporto per lo spostamento delle risorse](../../azure-resource-manager/management/move-support-resources.md).
    > [!IMPORTANT]
    >  - Azure CSP supporta solo risorse di Azure Resource Manager. Se una risorsa di Azure della sottoscrizione di origine è stata creata con il modello di distribuzione classica di Azure, prima della migrazione è necessario spostarla ad [Azure Resource Manager](/azure/cloud-solution-provider/migration/ea-payg-to-azure-csp/ea-open-direct-asm-to-arm). È necessario essere un partner per visualizzare la pagina Web.

1. Verificare che tutti i servizi di sottoscrizione di origine usino il modello di Azure Resource Manager. Trasferire quindi le risorse dalla sottoscrizione di origine alla sottoscrizione di destinazione usando [Azure Resource Move](../../azure-resource-manager/management/move-resource-group-and-subscription.md).
    > [!IMPORTANT]
    >  - Il trasferimento di risorse di Azure tra sottoscrizioni può causare tempi di inattività del servizio, in base alle risorse presenti nelle sottoscrizioni.

## <a name="transfer-csp-subscription-to-other-offer"></a>Trasferire la sottoscrizione CSP a un'altra offerta

Per trasferire altre sottoscrizioni da un partner CSP a qualunque altra offerta di Azure, il sottoscrittore deve spostare le risorse tra le sottoscrizioni CSP di origine e le sottoscrizioni CSP di destinazione.

1. Creare sottoscrizioni di Azure di destinazione.
1. Verificare che le sottoscrizioni di origine e di destinazione si trovino nello stesso tenant di Azure Active Directory (Azure AD). Per altre informazioni sulla modifica di un tenant Azure AD, vedere [Associare o aggiungere una sottoscrizione di Azure al tenant di Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).
    Si noti che la directory da modificare non è la sottoscrizione CSP. Ad esempio, se si esegue il trasferimento da una sottoscrizione CSP a una sottoscrizione con pagamento in base al consumo, è necessario modificare la directory della sottoscrizione con pagamento in base al consumo affinché corrisponda.

    > [!IMPORTANT]
    >  - Quando si associa una sottoscrizione a una directory diversa, gli utenti che dispongono di ruoli assegnati che usano [Controllo degli accessi in base al ruolo di Azure](../../role-based-access-control/role-assignments-portal.md) perdono l'accesso. Anche gli amministratori delle sottoscrizioni classiche, tra cui l'amministratore del servizio e i coamministratori, perdono l'accesso.
    >  - Anche le assegnazioni dei criteri vengono rimosse da una sottoscrizione quando la sottoscrizione è associata a una directory diversa.

1. L'account utente usato per il trasferimento deve disporre dell'accesso come proprietario di [Controllo degli accessi in base al ruolo di Azure](add-change-subscription-administrator.md) per entrambe le sottoscrizioni.
1. Prima di iniziare, [convalidare](/rest/api/resources/resources/validatemoveresources) l'opzione secondo cui tutte le risorse di Azure possono essere spostate dalla sottoscrizione di origine alla sottoscrizione di destinazione.
    > [!IMPORTANT]
    >  - Alcune risorse di Azure non possono essere spostate tra le sottoscrizioni. Per visualizzare l'elenco completo di risorse di Azure che possono essere spostate, vedere [Supporto per lo spostamento delle risorse](../../azure-resource-manager/management/move-support-resources.md).

1. Trasferire le risorse dalla sottoscrizione di origine alla sottoscrizione di destinazione usando [Azure Resource Move](../../azure-resource-manager/management/move-resource-group-and-subscription.md).
    > [!IMPORTANT]
    >  - Il trasferimento di risorse di Azure tra sottoscrizioni può causare tempi di inattività del servizio, in base alle risorse presenti nelle sottoscrizioni.

## <a name="next-steps"></a>Passaggi successivi
- [Ottenere la proprietà della fatturazione delle sottoscrizioni di Azure per l'account del Contratto Microsoft Partner](mpa-request-ownership.md).
- Leggere le informazioni su come [Gestire account e sottoscrizioni con la fatturazione di Azure](../index.yml).