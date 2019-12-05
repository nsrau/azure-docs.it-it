---
layout: LandingPage
description: Informazioni su come risolvere i problemi delle distribuzioni di macchine virtuali.
title: Documentazione sulla risoluzione dei problemi delle macchine virtuali di Azure | Microsoft Docs
services: virtual-machines
author: genlin
manager: dcscontentpm
ms.assetid: ''
ms.service: virtual-machines
ms.tgt_pltfrm: na
ms.topic: landing-page
ms.date: 10/3/2018
ms.author: genli
ms.openlocfilehash: bdb459a69557269a20d36f05acc2da502064efb9
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851350"
---
# <a name="troubleshooting-azure-virtual-machines"></a>Risoluzione dei problemi delle macchine virtuali di Azure

## <a name="tools-for-troubleshooting"></a>Strumenti per la risoluzione dei problemi

- [Console seriale](serial-console-overview.md)
- [Diagnostica di avvio](boot-diagnostics.md)
- [VM Windows: collegare il disco del sistema operativo a un'altra VM per la risoluzione di problemi](troubleshoot-recovery-disks-portal-windows.md)
- [VM Linux: collegare il disco del sistema operativo a un'altra VM per la risoluzione di problemi](troubleshoot-recovery-disks-portal-linux.md)

## <a name="cant-connect-to-the-vm"></a>Non è possibile connettersi alla VM

### <a name="windows"></a>Windows

**Soluzione comune**

- [Reimpostare RDP](reset-rdp.md)
- [Risoluzione dei problemi di RDP](troubleshoot-rdp-connection.md)
- [Risoluzione dettagliata dei problemi di RDP](detailed-troubleshoot-rdp.md)

**Errori di RDP**

- [Nessuna licenza server](troubleshoot-rdp-no-license-server.md)
- [Errore interno ](Troubleshoot-rdp-internal-error.md)
- [Errori di autenticazione](troubleshoot-authentication-error-rdp-vm.md)
- [Risolvere errori specifici ](troubleshoot-specific-rdp-errors.md)

**Errori di avvio di VM**

* [Errori di avvio di BitLocker](troubleshoot-bitlocker-boot-error.md)
* [All'avvio viene indicato il controllo in corso del file system](troubleshoot-check-disk-boot-error.md)
* [Errori con schermata blu](troubleshoot-common-blue-screen-error.md)
* [L'avvio della VM si blocca su un messaggio analogo a "Preparazione di Windows](troubleshoot-vm-boot-configure-update.md)
* [Errore "SI È VERIFICATO UN ERRORE DI UN SERVIZIO CRITICO" sulla schermata blu](troubleshoot-critical-service-failed-boot-error.md)
* [Problema del ciclo di riavvio](troubleshoot-reboot-loop.md)
* [L'avvio di una macchina virtuale di Azure si blocca nella fase di Windows Update](troubleshoot-stuck-updating-boot-error.md)
* [Avvio di VM in modalità provvisoria](troubleshoot-rdp-safe-mode.md)

**Altri**
- [Reimpostare la password di VM Windows offline](reset-local-password-without-agent.md)
- [Reimpostare la scheda di interfaccia di rete dopo una configurazione errata](reset-network-interface.md)

### <a name="linux"></a>Linux

- [Risoluzione dei problemi di SSH](troubleshoot-ssh-connection.md)
- [Risoluzione dettagliata dei problemi di SSH](detailed-troubleshoot-ssh-connection.md)
- [Messaggi di errore comuni](error-messages.md)
- [Reimpostare la password di VM Linux offline](reset-password.md)

## <a name="vm-deployment-issues"></a>Problemi di distribuzione di VM

- [Errori di allocazione](allocation-failure.md)
- Ridistribuire una VM
    - [Linux](redeploy-to-new-node-linux.md)
    - [Windows](redeploy-to-new-node-windows.md)
- Risolvere i problemi delle distribuzioni
    - [Linux](troubleshoot-deploy-vm-linux.md)
    - [Windows](troubleshoot-deploy-vm-windows.md)
- [Modifiche ai nomi dei dispositivi](troubleshoot-device-names-problems.md)
- [ Installare l'agente di macchine virtuali Windows offline](install-vm-agent-offline.md)
- [Riavvio o ridimensionamento di una VM](restart-resize-error-troubleshooting.md)

## <a name="vm-performance-issue"></a>Problema di prestazioni della VM
- [Problemi di prestazioni con le macchine virtuali](performance-diagnostics.md)
- Windows
    - [Come usare PerfInsights](how-to-use-perfinsights.md)
    - [Estensione di diagnostica delle prestazioni](performance-diagnostics-vm-extension.md)
- Linux
    - [Come usare PerfInsights](how-to-use-perfinsights-linux.md)