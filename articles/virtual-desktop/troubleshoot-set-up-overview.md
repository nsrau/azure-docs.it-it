---
title: Panoramica sulla risoluzione dei problemi di desktop virtuale Windows-Azure
description: Panoramica per la risoluzione dei problemi durante la configurazione di un ambiente tenant di desktop virtuali Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 11/09/2019
ms.author: helohr
ms.openlocfilehash: 7f80fb07bbed3bdb478bce557241a92cd571cc83
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904891"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>Panoramica della risoluzione dei problemi, feedback e supporto

Questo articolo fornisce una panoramica dei problemi che possono verificarsi durante la configurazione di un ambiente tenant di desktop virtuali Windows e fornisce modi per risolvere i problemi.

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti

Visitare la pagina [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) per discutere del servizio Desktop virtuale Windows con il team del prodotto e i membri attivi della community.

## <a name="escalation-tracks"></a>Tracce di escalation

Usare la tabella seguente per identificare e risolvere i problemi che possono verificarsi durante la configurazione di un ambiente tenant usando Desktop remoto client. Una volta configurata la configurazione del tenant, è possibile usare il nuovo [servizio di diagnostica](https://docs.microsoft.com/azure/virtual-desktop/diagnostics-role-service) per identificare i problemi per gli scenari comuni.

>[!NOTE]
> È disponibile un forum della community tecnica che è possibile visitare per discutere i problemi con il team del prodotto e i membri della community attiva. Visita il [desktop virtuale di Windows Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) 

| **Problema**                                                            | **Soluzione suggerita**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Creazione di un tenant                                                    | Se si verifica un'interruzione di Azure, contattare il [supporto tecnico di Azure](https://azure.microsoft.com/support/options/). in caso contrario, **aprire una richiesta di supporto per desktop virtuale Windows (Compute)** .|
| Accesso ai modelli di Marketplace in portale di Azure       | Se si verifica un'interruzione di Azure, contattare il [supporto tecnico di Azure](https://azure.microsoft.com/support/options/). <br> <br> I modelli di desktop virtuali Windows di Azure Marketplace sono disponibili gratuitamente.|
| Accesso ai modelli di Azure Resource Manager da GitHub                                  | Vedere la sezione "creazione di VM host sessione desktop virtuale Windows" della [creazione di tenant e pool host](troubleshoot-set-up-issues.md). Se il problema non è ancora risolto, contattare il [team di supporto di GitHub](https://github.com/contact). <br> <br> Se l'errore si verifica dopo aver eseguito l'accesso al modello in GitHub, contattare il [supporto tecnico di Azure](https://azure.microsoft.com/support/options/).|
| Pool host sessione-impostazioni della rete virtuale di Azure (VNET) e Express Route               | Contattare il **supporto tecnico di Azure (rete)** . |
| Creazione della macchina virtuale (VM) del pool host della sessione quando non vengono usati Azure Resource Manager modelli forniti con il desktop virtuale Windows | Contattare il **supporto tecnico di Azure (calcolo)** . <br> <br> Per problemi relativi ai modelli di Azure Resource Manager forniti con desktop virtuale di Windows, vedere la sezione relativa alla creazione di un tenant desktop virtuale Windows per la [creazione di tenant e pool host](troubleshoot-set-up-issues.md). |
| Gestione dell'ambiente host sessione desktop virtuale Windows dal portale di Azure    | Contattare il **supporto tecnico di Azure**. <br> <br> Per problemi di gestione quando si usa PowerShell per desktop virtuale Servizi Desktop remoto/Windows, vedere [desktop virtuale di Windows PowerShell](troubleshoot-powershell.md) o **aprire una richiesta di supporto per desktop virtuale Windows (calcolo)** . |
| Gestione della configurazione del desktop virtuale di Windows associata ai pool di host e ai gruppi di applicazioni (gruppi di app)      | Vedere [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)o **aprire una richiesta di supporto per desktop virtuale Windows (calcolo)** . <br> <br> Se i problemi sono legati all'interfaccia utente grafica (GUI) di esempio, rivolgersi alla community di Yammer.|
| Malfunzionamento dei client desktop remoto all'avvio                                                 | Vedere [Desktop remoto connessioni client](troubleshoot-client-connection.md) e, se il problema persiste, **aprire una richiesta di supporto per desktop virtuale Windows (calcolo)** .  <br> <br> Se si tratta di un problema di rete, gli utenti devono contattare l'amministratore di rete. |
| Connessione senza feed                                                                 | Risolvere i problemi usando la sezione "connessione utente ma nessuna visualizzazione (nessun feed)" di [Desktop remoto le connessioni client](troubleshoot-client-connection.md). <br> <br> Se gli utenti sono stati assegnati a un gruppo di app, **aprire una richiesta di supporto per desktop virtuale Windows (calcolo)** . |
| Problemi di individuazione dei feed dovuti alla rete                                            | Gli utenti devono contattare l'amministratore di rete. |
| Connessione dei client                                                                    | Vedere [Desktop remoto connessioni client](troubleshoot-client-connection.md) e, se il problema persiste, vedere la pagina relativa alla [configurazione della macchina virtuale host sessione](troubleshoot-vm-configuration.md). |
| Velocità di risposta delle applicazioni remote o del desktop                                      | Se i problemi sono collegati a un'applicazione o a un prodotto specifico, contattare il team responsabile del prodotto. |
| Messaggi di licenza o errori                                                          | Se i problemi sono collegati a un'applicazione o a un prodotto specifico, contattare il team responsabile del prodotto. |
| Problemi relativi all'uso di strumenti desktop virtuali di Windows su GitHub (modelli di Azure Resource Manager, strumento di diagnostica, strumento di gestione) | Vedere [modelli ARM per Servizi Desktop remoto](https://github.com/Azure/RDS-Templates/blob/master/README.md) per segnalare problemi. |

## <a name="next-steps"></a>Passaggi successivi

- Per risolvere i problemi durante la creazione di un tenant e di un pool host in un ambiente desktop virtuale Windows, vedere [creazione di tenant e pool host](troubleshoot-set-up-issues.md).
- Per risolvere i problemi durante la configurazione di una macchina virtuale (VM) in desktop virtuale di Windows, vedere [configurazione della macchina virtuale host sessione](troubleshoot-vm-configuration.md).
- Per risolvere i problemi relativi alle connessioni client di desktop virtuali Windows, vedere [Desktop remoto connessioni client](troubleshoot-client-connection.md).
- Per risolvere i problemi relativi all'uso di PowerShell con desktop virtuale di Windows, vedere [PowerShell per desktop virtuale di Windows](troubleshoot-powershell.md).
- Per ulteriori informazioni sul servizio, vedere [ambiente desktop virtuale di Windows](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Per un'esercitazione per la risoluzione dei problemi, vedere [esercitazione: risolvere i problemi relativi alle distribuzioni di modelli gestione risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Per altre informazioni sulle azioni di controllo, vedere [Operazioni di controllo con Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Per informazioni sulle azioni per determinare gli errori durante la distribuzione, vedere [visualizzare le operazioni di distribuzione](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).
