---
title: Connetti macchine ibride ad Azure dal centro di amministrazione di Windows
description: Questo articolo illustra come installare l'agente e connettere i computer ad Azure usando i server abilitati per Azure Arc dall'interfaccia di amministrazione di Windows.
ms.date: 10/12/2020
ms.topic: conceptual
ms.openlocfilehash: 8fa118a254689feade2897f083bb3aa2dc9db02c
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183414"
---
# <a name="connect-hybrid-machines-to-azure-from-windows-admin-center"></a>Connetti macchine ibride ad Azure dal centro di amministrazione di Windows

È possibile abilitare i server abilitati per Azure Arc per uno o più computer Windows nell'ambiente eseguendo manualmente un set di passaggi. In alternativa, è possibile usare l'interfaccia di [amministrazione di Windows](/windows-server/manage/windows-admin-center/understand/what-is) per distribuire l'agente del computer connesso e registrare i server locali senza dover eseguire alcun passaggio all'esterno di questo strumento.

## <a name="prerequisites"></a>Prerequisiti

* Server abilitati per Arc: esaminare i [prerequisiti](agent-overview.md#prerequisites) e verificare che la sottoscrizione, l'account Azure e le risorse soddisfino i requisiti.

* Interfaccia di amministrazione di Windows: esaminare i requisiti per [preparare l'ambiente per la](/windows-server/manage/windows-admin-center/deploy/prepare-environment) distribuzione e la [configurazione dell'integrazione di Azure ](/windows-server/manage/windows-admin-center/azure/azure-integration).

* Una sottoscrizione di Azure. Se non se ne ha una, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

* I server Windows di destinazione che si desidera gestire devono disporre della connettività Internet per accedere ad Azure.

### <a name="security"></a>Sicurezza

Per questo metodo di distribuzione è necessario disporre dei diritti di amministratore sul computer Windows o sul server di destinazione per installare e configurare l'agente. È anche necessario essere un membro del ruolo [**utenti gateway**](/windows-server/manage/windows-admin-center/plan/user-access-options#gateway-access-roles) .

## <a name="deploy"></a>Distribuisci

Per configurare Windows Server con i server abilitati per Arc, seguire questa procedura.

1. Accedere al centro di amministrazione di Windows.

1. Dall'elenco connessione nella pagina **Panoramica** , nell'elenco dei server Windows connessi, selezionare un server dall'elenco a cui connettersi.

1. Dal riquadro a sinistra selezionare **servizi ibridi di Azure**.

1. Nella pagina **servizi ibridi di Azure** selezionare **individua i servizi di Azure**.

1. Nella pagina **individua i servizi di Azure** , in **sfruttare i criteri e le soluzioni di Azure per gestire i server con Azure Arc**, selezionare **imposta**.

1. Nella pagina **Settings\Azure Arc for Servers** , se viene richiesto di eseguire l'autenticazione in Azure e **quindi selezionare inizia**.

1. Nella pagina **Connetti server ad Azure** , specificare quanto segue:

    1. Nell'elenco a discesa **sottoscrizione Azure** selezionare la sottoscrizione di Azure.
    1. Per **gruppo di risorse** selezionare **nuovo** per creare un nuovo gruppo di risorse o nell'elenco a discesa **gruppo di risorse** selezionare un gruppo di risorse esistente da cui registrare e gestire il computer.
    1. Nell'elenco a discesa **Area** selezionare l'area di Azure in cui archiviare i metadati dei server.
    1. Se il computer o il server sta comunicando tramite un server proxy per la connessione a Internet, selezionare l'opzione **Usa server proxy**. Specificare l'indirizzo IP del server proxy o il nome e il numero di porta che il computer utilizzerà per comunicare con il server proxy.

1. Selezionare **Configura** per procedere con la configurazione di Windows Server con i server abilitati per Azure Arc.

Windows Server si connette ad Azure, Scarica l'agente del computer connesso, lo installa e registra con i server abilitati per Azure Arc. Per tenere traccia dello stato di avanzamento, scegliere **notifiche** dal menu.

Per confermare l'installazione dell'agente del computer connesso, nell'interfaccia di amministrazione di Windows selezionare [**eventi**](/windows-server/manage/windows-admin-center/use/manage-servers#events) nel riquadro a sinistra per esaminare gli eventi *MsiInstaller* nel registro eventi dell'applicazione.

## <a name="verify-the-connection-with-azure-arc"></a>Verificare la connessione con Azure Arc

Dopo aver installato l'agente e averlo configurato per la connessione ai server abilitati per Azure Arc, passare al portale di Azure per verificare che il server sia stato connesso correttamente. Visualizzare la propria macchina virtuale nel [portale di Azure](https://portal.azure.com).

:::image type="content" source="./learn/media/quick-enable-hybrid-vm/enabled-machine.png" alt-text="Connessione alla macchina riuscita" border="false":::

## <a name="next-steps"></a>Passaggi successivi

* Le informazioni sulla risoluzione dei problemi sono reperibili nella [Guida alla risoluzione dei problemi relativi all'agente del computer connesso](troubleshoot-agent-onboard.md).

* Informazioni su come gestire il computer usando i [criteri di Azure](../../governance/policy/overview.md), ad esempio la configurazione di VM [Guest](../../governance/policy/concepts/guest-configuration.md), verificare che il computer stia segnalando l'area di lavoro Log Analytics prevista, abilitare il monitoraggio con [Monitoraggio di Azure con macchine virtuali](../../azure-monitor/insights/vminsights-enable-policy.md) e molto altro ancora.

* Altre informazioni sull'[agente Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). L'agente di Log Analytics per Windows e Linux è necessario quando si desidera raccogliere dati di monitoraggio del carico di lavoro e del sistema operativo, gestirli con manuali operativi di automazione o funzionalità come Gestione aggiornamenti o usare altri servizi di Azure come il [Centro sicurezza di Azure](../../security-center/security-center-introduction.md).