---
title: Panoramica della risoluzione dei problemi di Windows Virtual Desktop - Azure
description: Panoramica della risoluzione dei problemi durante l'impostazione di un ambiente tenant di Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 02/07/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 2b9dfb2ee7e74f94bfc3e6d2c679cb7da3d6bc66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127395"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>Panoramica della risoluzione dei problemi, feedback e supporto

In questo articolo viene fornita una panoramica dei problemi che possono verificarsi durante la configurazione di un ambiente tenant di Windows Virtual Desktop e vengono fornite modalità per risolvere i problemi.

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti

Visitare la pagina [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) per discutere del servizio Desktop virtuale Windows con il team del prodotto e i membri attivi della community.

## <a name="escalation-tracks"></a>Tracce di escalation

Utilizzare la tabella seguente per identificare e risolvere i problemi che possono verificarsi durante la configurazione di un ambiente tenant tramite client Desktop remoto. Dopo la configurazione del tenant, è possibile usare il nuovo [servizio di diagnostica](diagnostics-role-service.md) per identificare i problemi relativi agli scenari comuni.

>[!NOTE]
> Abbiamo un forum Tech Community che puoi visitare per discutere i tuoi problemi con il team del prodotto e i membri attivi della community. Visita la community tecnica di [Windows Virtual Desktop](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) per avviare una discussione.

| **Problema**                                                            | **Soluzione suggerita**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Creazione di un tenant di Windows Virtual Desktop                                                    | Se si verifica un'interruzione di Azure, aprire una richiesta di supporto di [Azure.](https://azure.microsoft.com/support/create-ticket/) in caso [contrario, aprire una richiesta](https://azure.microsoft.com/support/create-ticket/)di supporto di Azure , selezionare Desktop virtuale di **Windows** per il servizio, selezionare **Distribuzione** per il tipo di problema, quindi selezionare Problemi durante la creazione di un tenant di Windows **Virtual Desktop** per il sottotipo di problema.|
| Accesso ai modelli di Marketplace nel portale di AzureAccessing Marketplace templates in Azure portal       | Se si verifica un'interruzione di Azure, aprire una richiesta di supporto di Azure.If there's an Azure outage, [open an Azure support request](https://azure.microsoft.com/support/create-ticket/). <br> <br> I modelli di Desktop virtuale Windows di Azure Marketplace sono disponibili gratuitamente.|
| Accesso ai modelli di Azure Resource Manager da GitHubAccessing Azure Resource Manager templates from GitHub                                  | Vedere la sezione [Creazione di macchine virtuali host sessione Desktop virtuale](troubleshoot-set-up-issues.md#creating-windows-virtual-desktop-session-host-vms) di Windows di Creazione tenant e pool [host](troubleshoot-set-up-issues.md). Se il problema persiste, contattare il team di [supporto GitHub](https://github.com/contact). <br> <br> Se l'errore si verifica dopo l'accesso al modello in GitHub, contattare il [supporto di Azure](https://azure.microsoft.com/support/create-ticket/).|
| Impostazioni di rete virtuale (VNET) del pool host di sessione               | [Aprire una richiesta](https://azure.microsoft.com/support/create-ticket/)di supporto di Azure , quindi selezionare il servizio appropriato (nella categoria Rete). |
| Creazione di macchine virtuali (VM) del pool host di sessione quando i modelli di Azure Resource Manager forniti con Windows Virtual Desktop non vengono usatiSession host pool Virtual Machine (VM) creation when Azure Resource Manager templates provided with Windows Virtual Desktop are't being used | [Aprire una richiesta](https://azure.microsoft.com/support/create-ticket/)di supporto di Azure , quindi selezionare **Macchina virtuale che esegue Windows** per il servizio. <br> <br> Per problemi con i modelli di Azure Resource Manager forniti con Windows Virtual Desktop, vedere Sezione Creazione del tenant di Windows Virtual Desktop di [Creazione tenant tenant e pool host](troubleshoot-set-up-issues.md). |
| Gestione dell'ambiente host sessione di Windows Virtual Desktop dal portale di AzureManaging Windows Virtual Desktop session host environment from the Azure portal    | Aprire una richiesta di [supporto di Azure.](https://azure.microsoft.com/support/create-ticket/) <br> <br> Per problemi di gestione quando si usa Servizi Desktop remoto/Windows Virtual Desktop PowerShell, vedere [PowerShell](troubleshoot-powershell.md) di Windows Virtual Desktop o aprire una richiesta di [supporto](https://azure.microsoft.com/support/create-ticket/)di Azure , selezionare Desktop virtuale **di Windows** per il servizio, selezionare Configurazione e **gestione** per il tipo di problema, quindi selezionare Problemi durante la configurazione del **tenant tramite PowerShell** per il sottotipo di problema. |
| Gestione della configurazione di Windows Virtual Desktop legata ai pool host e ai gruppi di applicazioni (gruppi di app)      | Vedere [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)o aprire una richiesta di supporto di [Azure](https://azure.microsoft.com/support/create-ticket/), selezionare Desktop virtuale **di Windows** per il servizio, quindi selezionare il tipo di problema appropriato.|
| Distribuzione e gestione dei contenitori di profili FSLogixDeploying and manage FSLogix Profile Containers | Vedere Guida alla risoluzione dei problemi per i [prodotti FSLogix](/fslogix/fslogix-trouble-shooting-ht/) e, se il problema persiste, Aprire una richiesta di supporto di [Azure](https://azure.microsoft.com/support/create-ticket/), selezionare Desktop **virtuale Windows** per il servizio, selezionare **FSLogix** per il tipo di problema, quindi selezionare il sottotipo di problema appropriato. |
| Client desktop remoto malfunzionamento all'avvio                                                 | Vedere [Risolvere i problemi del client Desktop remoto](troubleshoot-client.md) e, se il problema persiste, Aprire una richiesta di supporto di [Azure](https://azure.microsoft.com/support/create-ticket/), selezionare Desktop virtuale **Windows** per il servizio, quindi selezionare Client **Desktop remoto** per il tipo di problema.  <br> <br> Se si tratta di un problema di rete, gli utenti devono contattare l'amministratore di rete. |
| Connesso ma senza alimentazione                                                                 | Risolvere i problemi relativi all'utilizzo della connessione [dell'utente, ma non viene visualizzato nulla (nessun feed)](troubleshoot-service-connection.md#user-connects-but-nothing-is-displayed-no-feed) delle connessioni al [servizio Desktop virtuale](troubleshoot-service-connection.md)di Windows . <br> <br> Se gli utenti sono stati assegnati a un gruppo di app, aprire una richiesta di supporto di [Azure](https://azure.microsoft.com/support/create-ticket/), selezionare Desktop virtuale di **Windows** per il servizio, quindi selezionare Client **Desktop remoto** per il tipo di problema. |
| Problemi di individuazione dei mangimi dovuti alla rete                                            | Gli utenti devono contattare l'amministratore di rete. |
| Connessione dei client                                                                    | Vedere Connessioni al [servizio Desktop virtuale di Windows](troubleshoot-service-connection.md) e, se il problema persiste, vedere Configurazione della macchina virtuale [dell'host di sessione](troubleshoot-vm-configuration.md). |
| Reattività delle applicazioni remote o desktop                                      | Se i problemi sono legati a un'applicazione o un prodotto specifico, contattare il team responsabile del prodotto. |
| Messaggi di licenza o errori                                                          | Se i problemi sono legati a un'applicazione o un prodotto specifico, contattare il team responsabile del prodotto. |
| Problemi durante l'utilizzo degli strumenti di Windows Virtual Desktop in GitHub (modelli di Azure Resource Manager, strumento di diagnostica, strumento di gestione)Issues when using Windows Virtual Desktop tools on GitHub (Azure Resource Manager templates, diagnostics tool, management tool) | Vedere Modelli di [Azure Resource Manager per Servizi Desktop remoto](https://github.com/Azure/RDS-Templates/blob/master/README.md) per segnalare problemi. |

## <a name="next-steps"></a>Passaggi successivi

- Per risolvere i problemi durante la creazione di un tenant e di un pool host in un ambiente Windows Virtual Desktop, vedere Creazione di [pool host e tenant](troubleshoot-set-up-issues.md).
- Per risolvere i problemi durante la configurazione di una macchina virtuale (VM) in Windows Virtual Desktop, vedere Configurazione della [macchina virtuale dell'host di sessione](troubleshoot-vm-configuration.md).
- Per risolvere i problemi relativi alle connessioni client di Desktop virtuale windows, vedere Connessioni al [servizio Desktop virtuale](troubleshoot-service-connection.md)di Windows .
- Per risolvere i problemi relativi ai client Desktop remoto, vedere [Risolvere i problemi relativi al client Desktop remoto](troubleshoot-client.md)
- Per risolvere i problemi relativi all'utilizzo di PowerShell con Windows Virtual Desktop, vedere [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Per ulteriori informazioni sul servizio, vedere [Ambiente Windows Virtual Desktop](environment-setup.md).
- Per eseguire un'esercitazione sulla risoluzione dei problemi, vedere [Esercitazione: Risolvere i problemi relativi alle distribuzioni](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)di modelli di Resource Manager .
- Per altre informazioni sulle azioni di controllo, vedere [Operazioni di controllo con Resource Manager](../azure-resource-manager/management/view-activity-logs.md).
- Per informazioni sulle azioni per determinare gli errori durante la distribuzione, vedere [Visualizzare le operazioni di distribuzione](../azure-resource-manager/templates/deployment-history.md).
