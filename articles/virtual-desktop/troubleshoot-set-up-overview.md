---
title: Windows Desktop virtuale la risoluzione dei problemi di panoramica, commenti e suggerimenti e supporto - Azure
description: Panoramica per la risoluzione dei problemi durante la configurazione di un ambiente di tenant di Desktop virtuale Windows.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshoot
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: 8e344d6908ba19f8e2294c7777b9c1016eafaf52
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64927643"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>Panoramica sulla risoluzione dei problemi, commenti e suggerimenti e supporto

Questo articolo offre una panoramica dei problemi si possono verificarsi quando si configura un ambiente di tenant di Desktop virtuale Windows e che offre metodi per risolvere i problemi.

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti

Al momento non vengono accettati casi di supporto mentre Desktop virtuale Windows è in anteprima. Visitare la pagina [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) per discutere del servizio Desktop virtuale Windows con il team del prodotto e i membri attivi della community.

## <a name="escalation-tracks"></a>Tiene traccia delle escalation

Usare la tabella seguente per identificare e risolvere i problemi che possono verificarsi quando si configura un ambiente di tenant tramite il client Desktop remoto.

>[!NOTE]
>Al momento non vengono accettati casi di supporto mentre Desktop virtuale Windows è in anteprima. Quando si fa riferimento al supporto tecnico di Windows Desktop virtuale, passare al forum Tech Community per il momento. Visitare il [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) per discutere di problemi con il team del prodotto e i membri della community attiva. Se è necessario risolvere una richiesta di supporto, includere l'ID attività e l'intervallo di tempo approssimativo per quando si è verificato il problema.

| **Problema**                                                            | **Soluzione suggerita**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Creazione di un Tenant                                                    | Se si verifica un'interruzione di Azure, contattare [supporto tecnico di Azure](https://azure.microsoft.com/support/options/); in caso contrario, contattare **supporto Remote Desktop Services/Windows Virtual Desktop**.|
| L'accesso ai modelli di Marketplace nel portale di Azure       | Se si verifica un'interruzione di Azure, contattare [supporto tecnico di Azure](https://azure.microsoft.com/support/options/). <br> <br> Modelli di Desktop virtuale Windows di Marketplace di Azure sono disponibili gratuitamente.|
| L'accesso ai modelli di Azure Resource Manager da GitHub                                  | Vedere la sezione "Creazione Windows virtuale host sessione Desktop le macchine virtuali" di [la creazione di pool di Tenant e l'host](troubleshoot-set-up-issues.md). Se il problema non è ancora risolto, contattare il [team di supporto di GitHub](https://github.com/contact). <br> <br> Se l'errore si verifica dopo l'accesso al modello in GitHub, contattare [supporto tecnico di Azure](https://azure.microsoft.com/support/options/).|
| Impostazioni host sessione pool Express Route e la rete virtuale di Azure (VNET)               | Contattare **supporto tecnico di Azure (rete)**. |
| Pool di host sessione la creazione di macchine virtuali (VM) quando non vengono utilizzati i modelli di Azure Resource Manager forniti con Windows Desktop virtuale | Contattare **supporto tecnico di Azure (calcolo)**. <br> <br> Per problemi con i modelli di Azure Resource Manager forniti con Windows Desktop virtuale, vedere la sezione di tenant di creazione di Desktop virtuale Windows della [la creazione di pool di Tenant e l'host](troubleshoot-set-up-issues.md). |
| La gestione di ambiente di host sessione Desktop virtuale Windows nel portale di Azure    | Contattare **supporto tecnico di Azure**. <br> <br> Per problemi di gestione quando si usa Remote Desktop Services/Windows virtuale-PowerShell Desktop, vedere [Windows PowerShell di Desktop virtuale](troubleshoot-powershell.md) oppure contattare il **team di supporto di Remote Desktop Services/Windows Desktop virtuale** . |
| Gestione della configurazione del Desktop virtuale di Windows associato ai pool di host e gruppi di applicazioni (gruppi di app)      | Visualizzare [Windows PowerShell di Desktop virtuale](troubleshoot-powershell.md), oppure contattare il **team di supporto di Remote Desktop Services/Windows Virtual Desktop**. <br> <br> Se i problemi sono legati all'interfaccia di utente con interfaccia grafica (GUI) di esempio, rivolgersi alla community di Yammer.|
| Malfunzionamento del client desktop remoto nel menu start                                                 | Visualizzare [le connessioni client Desktop remoto](troubleshoot-client-connection.md) se non viene risolto il problema, contattare **team di supporto di Remote Desktop Services/Windows Virtual Desktop**.  <br> <br> Se si tratta di un problema di rete, gli utenti devono contattare l'amministratore di rete. |
| Connesso ma alcun feed                                                                 | Risolvere i problemi usando il "utente si connette, ma viene visualizzato nulla (alcun feed)" sezione di [le connessioni client Desktop remoto](troubleshoot-client-connection.md). <br> <br> Se gli utenti assegnati a un gruppo di app, l'escalation per il **team di supporto di Remote Desktop Services/Windows Virtual Desktop**. |
| Feed dei problemi di individuazione a causa della rete                                            | Gli utenti devono contattare l'amministratore di rete. |
| La connessione dei client                                                                    | Visualizzare [le connessioni client Desktop remoto](troubleshoot-client-connection.md) se il problema persiste, vedere [configurazione della macchina virtuale host sessione](troubleshoot-vm-configuration.md). |
| Velocità di risposta delle applicazioni remote o desktop                                      | Se i problemi sono legati a una specifica applicazione o un prodotto, contattare il team responsabile del prodotto. |
| Errori o messaggi di gestione delle licenze                                                          | Se i problemi sono legati a una specifica applicazione o un prodotto, contattare il team responsabile del prodotto. |

## <a name="next-steps"></a>Passaggi successivi

- Per risolvere i problemi durante la creazione di un pool di tenant e l'host in un ambiente di Desktop virtuale Windows, vedere [la creazione di pool di Tenant e l'host](troubleshoot-set-up-issues.md).
- Per risolvere i problemi durante la configurazione di una macchina virtuale (VM) in Windows Desktop virtuale, vedere [configurazione della macchina virtuale host sessione](troubleshoot-vm-configuration.md).
- Per risolvere i problemi con le connessioni client di Desktop virtuale Windows, vedere [le connessioni client Desktop remoto](troubleshoot-client-connection.md).
- Per risolvere i problemi quando si usa PowerShell con Windows Desktop virtuale, vedere [Windows PowerShell di Desktop virtuale](troubleshoot-powershell.md).
- Per altre informazioni sul servizio di anteprima, vedere [ambiente di anteprima di Desktop Windows](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Per eseguire un'esercitazione di risoluzione dei problemi, vedere [Esercitazione: Risolvere i problemi di distribuzioni di modelli di Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Per altre informazioni sulle azioni di controllo, vedere [Operazioni di controllo con Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Per altre informazioni sulle azioni che consentono di determinare gli errori di distribuzione, vedere [Visualizzare le operazioni di distribuzione con il portale di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).