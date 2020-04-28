---
title: Abilitare il supporto per Avere vFXT - Azure
description: Come abilitare i caricamenti per il supporto da Avere vFXT per Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/14/2019
ms.author: rohogue
ms.openlocfilehash: d12bbd1708ceb948aea982f9ed1ab36879e3751c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75415359"
---
# <a name="enable-support-uploads"></a>Abilitare i caricamenti per il supporto

VFXT per Azure consente di caricare automaticamente i dati di supporto sul cluster. Questi caricamenti consentono al personale di supporto di fornire un servizio clienti ottimale.

## <a name="steps-to-enable-uploads"></a>Procedura di abilitazione dei caricamenti

Seguire questi passaggi dal pannello di controllo di Avere per attivare il supporto. Per informazioni su come aprire il pannello di controllo, vedere [accedere al cluster vFXT](avere-vfxt-cluster-gui.md) .

1. Passare alla scheda **Settings** (Impostazioni) in alto.
1. Fare clic sul collegamento **Support** (Supporto) a sinistra e accettare l'informativa sulla privacy.

   ![Screenshot che mostra il pannello di controllo di Avere e la finestra popup con il pulsante di conferma per accettare l'informativa sulla privacy](media/avere-vfxt-privacy-policy.png)

1. Nella pagina Configurazione supporto aprire la sezione **informazioni sul cliente** facendo clic sul triangolo a sinistra.
1. Fare clic sul pulsante **Revalidate upload information** (Riconvalida informazioni caricamento).
1. Impostare il nome del supporto del cluster nel **nome univoco del cluster**. Assicurarsi che questo nome identifichi in modo univoco il cluster per supportare il personale.
1. Selezionare le caselle relative a **Statistics Monitoring** (Monitoraggio statistiche), **General Information Upload** (Caricamento informazioni generali) e **Crash Information Upload** (Caricamento informazioni arresto anomalo).
1. Fare clic su **Submit** (Invia).

   ![Screenshot con la sezione delle informazioni del cliente completata nella pagina delle impostazioni di supporto](media/avere-vfxt-support-info.png)

1. Fare clic sul triangolo a sinistra di **Secure Proactive Support (SPS)** (Supporto proattivo sicuro (SPS)) per espandere la sezione.
1. Selezionare la casella **Enable SPS Link** (Abilita collegamento SPS).
1. Fare clic su **Submit** (Invia).

   ![Schermata contenente la sezione del supporto proattivo sicuro completata nella pagina delle impostazioni di supporto](media/avere-vfxt-support-sps.png)

## <a name="next-steps"></a>Passaggi successivi

Se è necessario aggiungere al cluster un sistema di archiviazione cloud locale o esistente, seguire le istruzioni in [configurare l'archiviazione](avere-vfxt-add-storage.md).

Se si è pronti per cominciare a collegare client al cluster, leggere [Montare il cluster Avere vFXT](avere-vfxt-mount-clients.md).
