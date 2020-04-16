---
title: 'Guida introduttiva: Creare avvisi personalizzati'
description: Informazioni per creare e assegnare avvisi del dispositivo personalizzati per il Centro sicurezza di Azure per il servizio di sicurezza per IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: d1757868-da3d-4453-803a-7e3a309c8ce8
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/27/2020
ms.author: mlottner
ms.openlocfilehash: 8ba81b669c25d91efa8ad1f07b1a7835b42c5cf4
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310841"
---
# <a name="quickstart-create-custom-alerts"></a>Guida introduttiva: Creare avvisi personalizzati

L'uso di gruppi di sicurezza e avvisi personalizzati sfrutta al meglio le informazioni di sicurezza end-to-end e la conoscenza categoriale dei dispositivi per garantire una maggiore sicurezza nell'intera soluzione IoT.

## <a name="why-use-custom-alerts"></a>Perché usare gli avvisi personalizzati?

Solo il proprietario conosce bene i propri dispositivi.

Per i clienti che conoscono perfettamente il comportamento previsto dei dispositivi, il Centro di sicurezza di Azure per IoT consente di tradurre questa conoscenza in criteri di comportamento dei dispositivi, avvisando in caso di eventuali deviazioni dalla norma.

## <a name="security-groups"></a>Gruppi di sicurezza

I gruppi di sicurezza consentono di definire gruppi logici di dispositivi e di gestirne lo stato della sicurezza in modo centralizzato.

Questi gruppi possono rappresentare i dispositivi con hardware specifico, i dispositivi distribuiti in un determinato percorso o qualsiasi altro gruppo adatto a esigenze specifiche.

I gruppi di sicurezza sono definiti da una proprietà tag dei dispositivi gemelli denominata **SecurityGroup**. Per impostazione predefinita, ogni soluzione IoT sull'hub IoT ha un gruppo di sicurezza denominato **default**. Per cambiare il gruppo di sicurezza di un dispositivo, cambiare il valore della proprietà **SecurityGroup**.

Ad esempio:

```
{
  "deviceId": "VM-Contoso12",
  "etag": "AAAAAAAAAAM=",
  "deviceEtag": "ODA1BzA5QjM2",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00",
  "connectionState": "Disconnected",
  "lastActivityTime": "0001-01-01T00:00:00",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "version": 4,
  "tags": {
    "SecurityGroup": "default"
  },
```

Usare i gruppi di sicurezza per raggruppare i dispositivi in categorie logiche. Dopo aver creato i gruppi, assegnarli agli avvisi personalizzati a scelta, per ottenere la soluzione di sicurezza per IoT end-to-end più efficace.

## <a name="customize-an-alert"></a>Personalizzare un avviso

1. Aprire l'hub IoT.
1. Fare clic su **Avvisi personalizzati** nella sezione **Sicurezza**.
1. Scegliere un gruppo di sicurezza a cui si vuole applicare la personalizzazione.
1. Fare clic su **Aggiungi un avviso personalizzato**.
1. Selezionare un avviso personalizzato nell'elenco a discesa.
1. Modificare le proprietà necessarie e fare clic su **OK**.
1. Assicurarsi di fare clic su **SALVA**. Se non si salva il nuovo avviso, l'avviso verrà eliminato la volta successiva che si chiude l'hub IoT.

## <a name="alerts-available-for-customization"></a>Avvisi disponibili per la personalizzazione

Il Centro sicurezza di Azure per IoT offre numerosi avvisi che possono essere personalizzati in base alle esigenze specifiche. Esaminare la [tabella degli avvisi personalizzabili](concept-customizable-security-alerts.md) per informazioni sulla gravità dell'avviso, l'origine dati, la descrizione e la procedura di correzione suggerita per ogni avviso ricevuto.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come distribuire un agente di sicurezza, passare all'articolo successivo...

> [!div class="nextstepaction"]
> [Distribuire un agente di sicurezza](how-to-deploy-agent.md)
