---
title: Abilitare il supporto per Avere vFXT - Azure
description: Come abilitare i caricamenti per il supporto da Avere vFXT per Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: bc21e06b704bfe3d25132092efbbf23f342acb14
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2018
ms.locfileid: "50669288"
---
# <a name="enable-support-uploads"></a>Abilitare i caricamenti per il supporto

Avere vFXT per Azure può caricare automaticamente dati di supporto sul cluster. Questi caricamenti consentono al personale di supporto di fornire un servizio clienti ottimale.

## <a name="steps-to-enable-uploads"></a>Procedura di abilitazione dei caricamenti

Seguire questi passaggi dal pannello di controllo di Avere per attivare il supporto. Leggere [Accedere al cluster vFXT](avere-vfxt-cluster-gui.md) per sapere come aprire il pannello di controllo di Avere.

1. Passare alla scheda **Settings** (Impostazioni) in alto.
1. Fare clic sul collegamento **Support** (Supporto) a sinistra e accettare l'informativa sulla privacy.

   ![Schermata di conferma dell'accettazione dell'informativa sulla privacy](media/avere-vfxt-privacy-policy.png)
1. Fare clic sul triangolo a sinistra di **Customer Info** (Info cliente) per espandere la sezione.
1. Impostare il nome del cluster per il supporto in **Unique Cluster Name** (Nome univoco cluster), assicurandosi che identifichi il cluster in modo univoco per il personale di supporto.
1. Selezionare le caselle relative a **Statistics Monitoring** (Monitoraggio statistiche), **General Information Upload** (Caricamento informazioni generali) e **Crash Information Upload** (Caricamento informazioni arresto anomalo).
1. Fare clic sul pulsante **Validate upload information** (Convalida informazioni caricamento).
1. Fare clic su **Submit**.
1. Fare clic sul triangolo a sinistra di **Secure Proactive Support (SPS)** (Supporto proattivo sicuro (SPS)) per espandere la sezione.
1. Selezionare la casella **Enable SPS Link** (Abilita collegamento SPS).
1. Fare clic su **Submit**.

   ![Screenshot contenente tutti i passaggi per abilitare il supporto](media/avere-vfxt-support-info-steps.png)


## <a name="next-steps"></a>Passaggi successivi

Se è necessario aggiungere un sistema di archiviazione locale al cluster, seguire le istruzioni in [Configure storage](avere-vfxt-add-storage.md) (Configurare lo spazio di archiviazione). 

Se si è pronti per cominciare a collegare client al cluster, leggere [Montare il cluster Avere vFXT](avere-vfxt-mount-clients.md).