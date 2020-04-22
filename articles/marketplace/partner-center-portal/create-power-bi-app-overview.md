---
title: Panoramica della creazione di app Power BI - Azure Marketplace
description: Questo articolo descrive i passaggi generali per la pubblicazione di un'app Power BI in Microsoft AppSource.This article describes the high-level steps for publishing a Power BI app to Microsoft AppSource. Vengono inoltre forniti i requisiti tecnici e aziendali che l'app Power BI deve soddisfare per essere pubblicata nel marketplace commerciale.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/02/2020
ms.openlocfilehash: 8f050a43cbdf4ab29df55cd5526eb231c301b271
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732669"
---
# <a name="power-bi-app-creation-overview"></a>Panoramica della creazione di app Power BI

> [!IMPORTANT]
> Stiamo spostando la gestione delle offerte dell'app Power BI dal portale per i partner cloud al Centro per i partner. Fino a quando non viene eseguita la migrazione delle offerte, seguire le istruzioni [nell'offerta](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/power-bi/cpp-power-bi-offer) di app Power BI per il portale per i partner cloud per gestire le offerte.

In questo articolo viene illustrato come pubblicare un'app Power BI in Microsoft [AppSource.](https://appsource.microsoft.com/) Un'app Power BI crea pacchetti di contenuto personalizzabile, inclusi set di dati, report e dashboard. È quindi possibile usare l'app con altre piattaforme di Power BI usando AppSource, eseguire le regolazioni e le personalizzazioni consentite dallo sviluppatore e connetterla ai propri dati.

## <a name="publishing-benefits"></a>Vantaggi della pubblicazione

Vantaggi della pubblicazione sul mercato commerciale:

- Promuovi la tua azienda utilizzando il marchio Microsoft.
- Raggiungi potenzialmente più di 100 milioni di utenti di Office 365 e Dynamics 365 su AppSource e più di 200.000 organizzazioni tramite Azure Marketplace.
- Ricevi lead di alta qualità da questi mercati.
- Fai in modo che i tuoi servizi vengano promossi dai team di settore e di televendita di Microsoft.

## <a name="overview"></a>Panoramica

:::image type="content" source="media/power-bi-app-publishing-steps.png" alt-text="Panoramica dei passaggi per pubblicare un'app Power BI" border="false":::

Questi sono i passaggi principali di pubblicazione:

1. Creare l'applicazione in Power BI. Riceverai un link di installazione del pacchetto, che è la principale risorsa tecnica per l'offerta. Inviare il pacchetto di test alla pre-produzione prima di creare l'offerta nel Centro per i partner. Per informazioni dettagliate, vedere [Che cosa sono le app Power BI?](https://docs.microsoft.com/power-bi/service-template-apps-overview).
2. Aggiungere il materiale di marketing, ad esempio il nome ufficiale, la descrizione e i loghi.
3. Includere i documenti legali e di supporto dell'offerta, ad esempio le condizioni per l'utilizzo, l'informativa sulla privacy, le norme di supporto e la Guida per gli utenti.
4. Creare l'offerta: utilizza il Centro per i partner per modificare i dettagli, tra cui la descrizione dell'offerta, il materiale di marketing, le informazioni legali, le informazioni di supporto e le specifiche delle risorse.
5. Inviarlo per la pubblicazione.
6. Monitora il processo nel Centro per i partner, in cui il team di onboarding di AppSource testa, convalida e certifica l'app.
7. Dopo la certificazione, esaminare l'app nel relativo ambiente di test e rilasciarla. Questo lo elencherà su AppSource (si "va in diretta").
8. In Power BI inviare il pacchetto nell'ambiente di produzione. Per informazioni dettagliate, vedere [Gestire la versione dell'app Power BI.](https://docs.microsoft.com/power-bi/service-template-apps-create#manage-the-template-app-release)

## <a name="before-you-begin"></a>Prima di iniziare

Esaminare i collegamenti seguenti, che forniscono modelli, suggerimenti ed esempi.

- [Creare un'app di Power BI](https://docs.microsoft.com/power-bi/service-template-apps-create)
- [Suggerimenti per la creazione di un'app Power BI](https://docs.microsoft.com/power-bi/service-template-apps-tips)
- [Esempi](https://docs.microsoft.com/power-bi/service-template-apps-samples)

## <a name="requirements"></a>Requisiti

Per essere pubblicata nel marketplace commerciale, l'offerta dell'app Power BI deve soddisfare i requisiti tecnici e aziendali seguenti.

### <a name="technical-requirements"></a>Requisiti tecnici

L'asset tecnico principale di cui hai bisogno è [un'app Power BI.](https://go.microsoft.com/fwlink/?linkid=2028636) Si tratta di una raccolta di set di dati, report o dashboard primari. Include inoltre servizi connessi facoltativi e set di dati incorporati, precedentemente noti come [pacchetto di contenuto.](https://docs.microsoft.com/power-bi/service-organizational-content-pack-introduction) Per altre informazioni sullo sviluppo di questo tipo di app, vedere [Che cosa sono le app Power BI?](https://go.microsoft.com/fwlink/?linkid=2028636).

#### <a name="get-an-installation-web-address"></a>Ottenere un indirizzo Web di installazioneGet an installation web address

È possibile creare solo un'app Power BI all'interno dell'ambiente [Power BI.](https://powerbi.microsoft.com/)

1. Accedere con una [licenza di Power BI Pro](https://docs.microsoft.com/power-bi/service-admin-purchasing-power-bi-pro).
2. Creare e testare l'app in Power BI.
3. Quando si riceve l'indirizzo Web di installazione dell'app, aggiungerlo alla pagina **Configurazione tecnica** nel Centro per i partner.

Dopo aver creato e testato l'app in Power BI, salvare l'indirizzo Web di installazione dell'applicazione, in base alle esigenze per [creare un'offerta](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-power-bi-app-offer)di app Power BI.

### <a name="business-requirements"></a>Requisiti aziendali

I requisiti aziendali includono obblighi procedurali, contrattuali e legali. È necessario:

- Essere un editore del marketplace commerciale registrato. Se non sei registrato, segui i passaggi descritti in [Diventa un editore](https://docs.microsoft.com/azure/marketplace/become-publisher)del Marketplace commerciale.
- Fornisci contenuti che soddisfino i criteri per la tua offerta da elencare su AppSource. Per altre informazioni, vedere [È necessario elencare un'app in AppSource? Ecco come](https://appsource.microsoft.com/blogs/have-an-app-to-list-on-appsource-here-s-how).
- Accettare e seguire [l'Informativa sulla privacy di Microsoft](https://privacy.microsoft.com/privacystatement).

## <a name="next-steps"></a>Passaggi successivi

- [Creare un'offerta di app Power BI nel Centro per i partner](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-power-bi-app-offer)