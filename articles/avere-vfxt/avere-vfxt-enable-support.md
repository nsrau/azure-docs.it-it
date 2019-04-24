---
title: Abilitare il supporto per Avere vFXT - Azure
description: Come abilitare i caricamenti per il supporto da Avere vFXT per Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: fe096b2e2a75cc89e3ce5ef905d8e4c347cc153a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60409837"
---
# <a name="enable-support-uploads"></a>Abilitare i caricamenti per il supporto

Avere vFXT per Azure può caricare automaticamente dati di supporto sul cluster. Questi caricamenti consentono al personale di supporto di fornire un servizio clienti ottimale.

## <a name="steps-to-enable-uploads"></a>Procedura di abilitazione dei caricamenti

Seguire questi passaggi dal pannello di controllo di Avere per attivare il supporto. Leggere [Accedere al cluster vFXT](avere-vfxt-cluster-gui.md) per sapere come aprire il pannello di controllo di Avere.

1. Passare alla scheda **Settings** (Impostazioni) in alto.
1. Fare clic sul collegamento **Support** (Supporto) a sinistra e accettare l'informativa sulla privacy.

   ![Screenshot che mostra il pannello di controllo di Avere e la finestra popup con il pulsante di conferma per accettare l'informativa sulla privacy](media/avere-vfxt-privacy-policy.png)

1. Fare clic sul triangolo a sinistra di **Customer Info** (Info cliente) per espandere la sezione.
1. Fare clic sul pulsante **Revalidate upload information** (Riconvalida informazioni caricamento).
1. Impostare il nome del cluster per il supporto in **Unique Cluster Name** (Nome univoco cluster), assicurandosi che identifichi il cluster in modo univoco per il personale di supporto.
1. Selezionare le caselle relative a **Statistics Monitoring** (Monitoraggio statistiche), **General Information Upload** (Caricamento informazioni generali) e **Crash Information Upload** (Caricamento informazioni arresto anomalo).
1. Fare clic su **Submit**.

   ![Screenshot con la sezione delle informazioni del cliente completata nella pagina delle impostazioni di supporto](media/avere-vfxt-support-info.png)

1. Fare clic sul triangolo a sinistra di **Secure Proactive Support (SPS)** (Supporto proattivo sicuro (SPS)) per espandere la sezione.
1. Selezionare la casella **Enable SPS Link** (Abilita collegamento SPS).
1. Fare clic su **Submit**.

   ![Schermata contenente la sezione del supporto proattivo sicuro completata nella pagina delle impostazioni di supporto](media/avere-vfxt-support-sps.png)

## <a name="next-steps"></a>Passaggi successivi

Se è necessario aggiungere un on-premise o esistenti di sistema di archiviazione al cluster del cloud, seguire le istruzioni in [configurare l'archiviazione](avere-vfxt-add-storage.md). 

Se si è pronti per cominciare a collegare client al cluster, leggere [Montare il cluster Avere vFXT](avere-vfxt-mount-clients.md).