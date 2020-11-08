---
title: Come aggiungere i dettagli tecnici per l'offerta di applicazione Azure
description: Informazioni su come aggiungere i dettagli tecnici per l'offerta di applicazione Azure nel centro per i partner.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: a8658242bca48ee490e7fd44582f46e7c9c78e5b
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94370237"
---
# <a name="how-to-add-technical-details-for-your-azure-application-offer"></a>Come aggiungere i dettagli tecnici per l'offerta di applicazione Azure

Questo articolo descrive come immettere i dettagli tecnici che consentono a Microsoft Commercial Marketplace di connettersi alla soluzione. Questa connessione consente di effettuare il provisioning dell'offerta per il cliente se sceglie di acquisirla e gestirla.

Completare questa sezione solo se l'offerta include un'applicazione gestita che emetterà eventi di misurazione usando le [API di fatturazione a consumo del Marketplace](partner-center-portal/marketplace-metering-service-apis.md) e un servizio che verrà autenticato con un token di sicurezza Azure ad. Per altre informazioni, vedere [strategie di autenticazione del servizio di misurazione del Marketplace](partner-center-portal/marketplace-metering-service-authentication.md) sulle diverse opzioni di autenticazione.

## <a name="technical-configuration-offer-level"></a>Configurazione tecnica (a livello di offerta)

La scheda **configurazione tecnica** si applica solo se si creerà un'applicazione gestita che genera eventi di misurazione usando le API di [fatturazione a consumo del Marketplace](partner-center-portal/marketplace-metering-service-apis.md). In tal caso, completare i passaggi seguenti. In caso contrario, andare alla [procedura successiva](#next-steps). 

Per ulteriori informazioni su questi campi, vedere [pianificare un'offerta applicazione Azure per il Marketplace commerciale](plan-azure-application-offer.md#technical-configuration).

1. Nella scheda **configurazione tecnica** specificare il **Azure Active Directory id tenant** e **Azure Active Directory ID applicazione** usato per convalidare la connessione tra i due servizi è dietro una comunicazione autenticata.

1. Selezionare **Salva bozza** prima di continuare con la scheda successiva: Panoramica del piano.

## <a name="next-steps"></a>Passaggi successivi

- [Come creare piani per l'offerta di applicazione Azure](create-new-azure-apps-offer-plans.md)
