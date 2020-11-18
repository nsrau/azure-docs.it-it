---
title: Risolvere i problemi di Monitoraggio di Azure per le macchine virtuali integrità Guest (anteprima)
description: Vengono descritte le procedure di risoluzione dei problemi che è possibile eseguire in caso di problemi con Monitoraggio di Azure per le macchine virtuali integrità.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/08/2020
ms.openlocfilehash: 048230bb85d2ac599efc6bd3fb326f98038cce57
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94687023"
---
# <a name="troubleshoot-azure-monitor-for-vms-guest-health-preview"></a>Risolvere i problemi di Monitoraggio di Azure per le macchine virtuali integrità Guest (anteprima)
Questo articolo descrive le procedure di risoluzione dei problemi che è possibile eseguire in caso di problemi con Monitoraggio di Azure per le macchine virtuali integrità.

## <a name="error-message-that-no-data-is-available"></a>Messaggio di errore che indica che non sono disponibili dati 

![Nessun dato](media/vminsights-health-troubleshoot/no-data.png)


### <a name="verify-that-the-virtual-machine-meets-configuration-requirements"></a>Verificare che la macchina virtuale soddisfi i requisiti di configurazione

1. Verificare che la macchina virtuale sia una macchina virtuale di Azure. Azure Arc per i server non è attualmente supportato.
2. Verificare che la macchina virtuale esegua un [sistema operativo supportato](vminsights-health-enable.md?current-limitations.md).
3. Verificare che la macchina virtuale sia installata in un' [area supportata](vminsights-health-enable.md?current-limitations.md).
4. Verificare che l'area di lavoro Log Analytics sia installata in un' [area supportata](vminsights-health-enable.md?current-limitations.md).

### <a name="verify-that-the-vm-is-properly-onboarded"></a>Verificare che la macchina virtuale sia stata caricata correttamente
Verificare che l'estensione agente di monitoraggio di Azure e l'agente integrità macchina virtuale guest siano stati correttamente sottoposti a provisioning nella macchina virtuale. Selezionare **estensioni** dal menu della macchina virtuale nel portale di Azure. Se vengono elencati i due agenti, vedere []() .

![Estensioni di VM](media/vminsights-health-troubleshoot/extensions.png)

### <a name="verify-the-system-assigned-identity-is-enabled-on-the-virtual-machine"></a>Verificare che l'identità assegnata dal sistema sia abilitata nella macchina virtuale
Verificare che l'identità assegnata dal sistema sia abilitata nella macchina virtuale. Selezionare **Identity** dal menu della macchina virtuale nel portale di Azure. Se vengono elencati i due agenti, vedere []() .

![Identità assegnata dal sistema](media/vminsights-health-troubleshoot/system-identity.png)

### <a name="verify-data-collection-rule"></a>Verificare la regola di raccolta dati
Verificare che la regola di raccolta dati che specifica l'estensione di integrità come origine dati sia associata alla macchina virtuale.

## <a name="error-message-for-bad-request-due-to-insufficient-permissions"></a>Messaggio di errore per una richiesta non valida a causa di autorizzazioni insufficienti
Questo errore indica che il provider di risorse **Microsoft. WorkloadMonitor** non è stato registrato nella sottoscrizione. Per informazioni dettagliate sulla registrazione del provider di risorse, vedere [provider e tipi di risorse di Azure](/azure/azure-resource-manager/management/resource-providers-and-types#register-resource-provider) . 

![Richiesta non valida](media/vminsights-health-troubleshoot/bad-request.png)

## <a name="next-steps"></a>Passaggi successivi

- [Ottenere una panoramica della funzionalità di integrità Guest di Monitoraggio di Azure per le macchine virtuali](vminsights-health-overview.md)
