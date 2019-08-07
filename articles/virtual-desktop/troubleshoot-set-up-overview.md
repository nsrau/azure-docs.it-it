---
title: Panoramica sulla risoluzione dei problemi di desktop virtuale Windows, commenti e suggerimenti e supporto-Azure
description: Panoramica per la risoluzione dei problemi durante la configurazione di un ambiente tenant di desktop virtuali Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: helohr
ms.openlocfilehash: e652c8a67db686159a2c3fd48e2ea65888bcc10d
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816343"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>Panoramica della risoluzione dei problemi, feedback e supporto

Questo articolo fornisce una panoramica dei problemi che possono verificarsi durante la configurazione di un ambiente tenant di desktop virtuali Windows e fornisce modi per risolvere i problemi.

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti

Al momento non vengono accettati casi di supporto mentre Desktop virtuale Windows è in anteprima. Visitare la pagina [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) per discutere del servizio Desktop virtuale Windows con il team del prodotto e i membri attivi della community.

## <a name="escalation-tracks"></a>Tracce di escalation

Usare la tabella seguente per identificare e risolvere i problemi che possono verificarsi durante la configurazione di un ambiente tenant usando Desktop remoto client. Una volta configurata la configurazione del tenant, è possibile usare il nuovo [servizio di diagnostica](https://docs.microsoft.com/azure/virtual-desktop/diagnostics-role-service) per identificare i problemi per gli scenari comuni.

>[!NOTE]
>Al momento non vengono accettati casi di supporto mentre Desktop virtuale Windows è in anteprima. Ogni volta che si fa riferimento al supporto per desktop virtuali Windows, per il momento passare al forum della community tecnica. Visitare il [desktop virtuale di Windows Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) per discutere di problemi con il team del prodotto e i membri della community attiva. Se è necessario risolvere un problema di supporto, includere l'ID attività e l'intervallo di tempo approssimativo in cui si è verificato il problema.

| **Problema**                                                            | **Soluzione suggerita**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Creazione di un tenant                                                    | Se si verifica un'interruzione di Azure, contattare il [supporto tecnico di Azure](https://azure.microsoft.com/support/options/). in caso contrario, contattare **Servizi Desktop remoto/supporto per desktop virtuali Windows**.|
| Accesso ai modelli di Marketplace in portale di Azure       | Se si verifica un'interruzione di Azure, contattare il [supporto tecnico di Azure](https://azure.microsoft.com/support/options/). <br> <br> I modelli di desktop virtuali Windows di Azure Marketplace sono disponibili gratuitamente.|
| Accesso ai modelli di Azure Resource Manager da GitHub                                  | Vedere la sezione "creazione di VM host sessione desktop virtuale Windows" della [creazione di tenant e pool host](troubleshoot-set-up-issues.md). Se il problema non è ancora risolto, contattare il [team di supporto di GitHub](https://github.com/contact). <br> <br> Se l'errore si verifica dopo aver eseguito l'accesso al modello in GitHub, contattare il [supporto tecnico di Azure](https://azure.microsoft.com/support/options/).|
| Pool host sessione-impostazioni della rete virtuale di Azure (VNET) e Express Route               | Contattare il **supporto tecnico di Azure (rete)** . |
| Creazione della macchina virtuale (VM) del pool host della sessione quando non vengono usati Azure Resource Manager modelli forniti con il desktop virtuale Windows | Contattare il **supporto tecnico di Azure (calcolo)** . <br> <br> Per problemi relativi ai modelli di Azure Resource Manager forniti con desktop virtuale di Windows, vedere la sezione relativa alla creazione di un tenant desktop virtuale Windows per la [creazione di tenant e pool host](troubleshoot-set-up-issues.md). |
| Gestione dell'ambiente host sessione desktop virtuale Windows dal portale di Azure    | Contattare il **supporto tecnico di Azure**. <br> <br> Per problemi di gestione quando si usa PowerShell per desktop virtuale Servizi Desktop remoto/Windows, vedere [PowerShell per desktop virtuale Windows](troubleshoot-powershell.md) o contattare il **team di supporto di Servizi Desktop remoto/Windows Virtual Desktop**. |
| Gestione della configurazione del desktop virtuale di Windows associata ai pool di host e ai gruppi di applicazioni (gruppi di app)      | Vedere [PowerShell per desktop virtuale di Windows](troubleshoot-powershell.md)oppure contattare il **team di supporto di Servizi Desktop remoto/Windows Virtual Desktop**. <br> <br> Se i problemi sono legati all'interfaccia utente grafica (GUI) di esempio, rivolgersi alla community di Yammer.|
| Malfunzionamento dei client desktop remoto all'avvio                                                 | Vedere [Desktop remoto connessioni client](troubleshoot-client-connection.md) e, se il problema persiste, contattare il **team di supporto di Servizi Desktop remoto/Windows Virtual Desktop**.  <br> <br> Se si tratta di un problema di rete, gli utenti devono contattare l'amministratore di rete. |
| Connessione senza feed                                                                 | Risolvere i problemi usando la sezione "connessione utente ma nessuna visualizzazione (nessun feed)" di [Desktop remoto le connessioni client](troubleshoot-client-connection.md). <br> <br> Se gli utenti sono stati assegnati a un gruppo di app, inoltrarsi al **team di supporto di Servizi Desktop remoto/Windows Virtual Desktop**. |
| Problemi di individuazione dei feed dovuti alla rete                                            | Gli utenti devono contattare l'amministratore di rete. |
| Connessione dei client                                                                    | Vedere [Desktop remoto connessioni client](troubleshoot-client-connection.md) e, se il problema persiste, vedere la pagina relativa alla [configurazione della macchina virtuale host sessione](troubleshoot-vm-configuration.md). |
| Velocità di risposta delle applicazioni remote o del desktop                                      | Se i problemi sono collegati a un'applicazione o a un prodotto specifico, contattare il team responsabile del prodotto. |
| Messaggi di licenza o errori                                                          | Se i problemi sono collegati a un'applicazione o a un prodotto specifico, contattare il team responsabile del prodotto. |

## <a name="next-steps"></a>Passaggi successivi

- Per risolvere i problemi durante la creazione di un tenant e di un pool host in un ambiente desktop virtuale Windows, vedere [creazione di tenant e pool host](troubleshoot-set-up-issues.md).
- Per risolvere i problemi durante la configurazione di una macchina virtuale (VM) in desktop virtuale di Windows, vedere [configurazione della macchina virtuale host sessione](troubleshoot-vm-configuration.md).
- Per risolvere i problemi relativi alle connessioni client di desktop virtuali Windows, vedere [Desktop remoto connessioni client](troubleshoot-client-connection.md).
- Per risolvere i problemi relativi all'uso di PowerShell con desktop virtuale di Windows, vedere [PowerShell per desktop virtuale di Windows](troubleshoot-powershell.md).
- Per ulteriori informazioni sul servizio di anteprima, vedere [ambiente Windows Desktop Preview](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Per eseguire un'esercitazione di risoluzione dei problemi, vedere [Esercitazione: Risolvere i problemi relativi alle](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot)distribuzioni gestione risorse modello.
- Per altre informazioni sulle azioni di controllo, vedere [Operazioni di controllo con Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Per altre informazioni sulle azioni che consentono di determinare gli errori di distribuzione, vedere [Visualizzare le operazioni di distribuzione con il portale di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).