---
title: Panoramica sulla risoluzione dei problemi di desktop virtuale Windows-Azure
description: Panoramica per la risoluzione dei problemi durante la configurazione di un ambiente desktop virtuale di Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 06/05/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 07857feb2e5552429c445b22c4c4b2f121f29a93
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84976490"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>Panoramica della risoluzione dei problemi, feedback e supporto

>[!IMPORTANT]
>Questo contenuto si applica all'aggiornamento di Primavera 2020 con gli oggetti Azure Resource Manager di Desktop virtuale Windows. Se si usa la versione Autunno 2019 di Desktop virtuale Windows senza gli oggetti Azure Resource Manager, vedere [questo articolo](./virtual-desktop-fall-2019/troubleshoot-set-up-overview-2019.md).
>
> L'aggiornamento di Primavera 2020 di Desktop virtuale Windows è attualmente disponibile in anteprima pubblica. Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. 
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

In questo articolo viene fornita una panoramica dei problemi che possono verificarsi durante la configurazione di un ambiente di desktop virtuale Windows e vengono fornite le modalità di risoluzione dei problemi.

## <a name="report-issues-during-public-preview"></a>Segnala problemi durante l'anteprima pubblica

Per segnalare problemi o suggerire funzionalità durante l'anteprima pubblica per la versione Spring 2020, visitare la pagina relativa a [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop). È possibile utilizzare la Community Tech per discutere le procedure consigliate o suggerire e votare le nuove funzionalità. Quando si segnala un problema relativo alla versione di anteprima pubblica, assicurarsi di etichettare il tipo di problema come **aggiornamento di Spring 2020 (anteprima)**.

Quando si effettua un post che richiede assistenza o si propone una nuova funzionalità, assicurarsi di descrivere il proprio argomento nel modo più dettagliato possibile. Le informazioni dettagliate possono aiutare gli altri utenti a rispondere alla domanda o a comprendere la funzionalità a cui si sta proponendo un voto.

## <a name="escalation-tracks"></a>Tracce di escalation

Prima di eseguire altre operazioni, assicurarsi di controllare la [pagina stato di Azure](https://status.azure.com/status) e l' [integrità dei servizi di Azure](https://azure.microsoft.com/features/service-health/) per assicurarsi che il servizio di Azure venga eseguito correttamente.

Usare la tabella seguente per identificare e risolvere i problemi che possono verificarsi durante la configurazione di un ambiente con Desktop remoto client. Una volta configurato l'ambiente, è possibile usare il nuovo [servizio di diagnostica](diagnostics-role-service.md) per identificare i problemi per gli scenari comuni.

| **Problema**                                                            | **Soluzione suggerita**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Pool host sessione-impostazioni della rete virtuale di Azure (VNET) e Express Route               | [Aprire una richiesta di supporto tecnico di Azure](https://azure.microsoft.com/support/create-ticket/), quindi selezionare il servizio appropriato (nella categoria rete). |
| Creazione della macchina virtuale (VM) del pool host della sessione quando non vengono usati Azure Resource Manager modelli forniti con il desktop virtuale Windows | [Aprire una richiesta di supporto tecnico di Azure](https://azure.microsoft.com/support/create-ticket/), quindi selezionare **desktop virtuale Windows** per il servizio. <br> <br> Per i problemi relativi ai modelli di Azure Resource Manager forniti con desktop virtuale di Windows, vedere la sezione Errori del modello di Azure Resource Manager della [creazione del pool host](troubleshoot-set-up-issues.md). |
| Gestione dell'ambiente host sessione desktop virtuale Windows dal portale di Azure    | [Aprire una richiesta di supporto tecnico di Azure](https://azure.microsoft.com/support/create-ticket/). <br> <br> Per problemi di gestione quando si usa PowerShell per desktop virtuale Servizi Desktop remoto/Windows, vedere [desktop virtuale Windows PowerShell](troubleshoot-powershell.md) o [aprire una richiesta di supporto tecnico di Azure](https://azure.microsoft.com/support/create-ticket/), selezionare **desktop virtuale Windows** per il servizio, selezionare **configurazione e gestione** per il tipo di problema, quindi selezionare **problemi configurazione dell'ambiente con PowerShell** per il sottotipo di problema. |
| Gestione della configurazione del desktop virtuale di Windows associata ai pool di host e ai gruppi di applicazioni (gruppi di app)      | Vedere [desktop virtuale di Windows PowerShell](troubleshoot-powershell.md)o [aprire una richiesta di supporto di Azure](https://azure.microsoft.com/support/create-ticket/), selezionare **desktop virtuale Windows** per il servizio, quindi selezionare il tipo di problema appropriato.|
| Distribuzione e gestione di contenitori di profili FSLogix | Vedere la [Guida alla risoluzione dei problemi per i prodotti FSLogix](/fslogix/fslogix-trouble-shooting-ht/) e se il problema persiste, [aprire una richiesta di supporto di Azure](https://azure.microsoft.com/support/create-ticket/), selezionare **desktop virtuale Windows** per il servizio, selezionare **FSLogix** per il tipo di problema, quindi selezionare il sottotipo di problema appropriato. |
| Malfunzionamento dei client desktop remoto all'avvio                                                 | Vedere [risolvere i problemi relativi al client di desktop remoto](troubleshoot-client.md) e, se il problema persiste, [aprire una richiesta di supporto di Azure](https://azure.microsoft.com/support/create-ticket/), selezionare **desktop virtuale Windows** per il servizio e quindi selezionare **Desktop remoto client** per il tipo di problema.  <br> <br> Se si tratta di un problema di rete, gli utenti devono contattare l'amministratore di rete. |
| Connessione senza feed                                                                 | Risolvere i problemi relativi all'utilizzo delle connessioni [utente, ma non viene visualizzata alcuna sezione (nessun feed)](troubleshoot-service-connection.md#user-connects-but-nothing-is-displayed-no-feed) delle [connessioni al servizio desktop virtuale di Windows](troubleshoot-service-connection.md). <br> <br> Se gli utenti sono stati assegnati a un gruppo di app, [aprire una richiesta di supporto di Azure](https://azure.microsoft.com/support/create-ticket/), selezionare **desktop virtuale Windows** per il servizio, quindi selezionare **Desktop remoto client** per il tipo di problema. |
| Problemi di individuazione dei feed dovuti alla rete                                            | Gli utenti devono contattare l'amministratore di rete. |
| Connessione dei client                                                                    | Vedere [connessioni al servizio desktop virtuale Windows](troubleshoot-service-connection.md) . se il problema persiste, vedere [configurazione della macchina virtuale host sessione](troubleshoot-vm-configuration.md). |
| Velocità di risposta delle applicazioni remote o del desktop                                      | Se i problemi sono collegati a un'applicazione o a un prodotto specifico, contattare il team responsabile del prodotto. |
| Messaggi di licenza o errori                                                          | Se i problemi sono collegati a un'applicazione o a un prodotto specifico, contattare il team responsabile del prodotto. |
| Problemi con i metodi di autenticazione di terze parti | Verificare che il provider di terze parti supporti scenari di desktop virtuale di Windows e che si avvicini a eventuali problemi noti. |
| Problemi di utilizzo di Log Analytics per desktop virtuale di Windows | Per problemi con lo schema di diagnostica, [aprire una richiesta di supporto di Azure](https://azure.microsoft.com/support/create-ticket/).<br><br>Per le query, la visualizzazione o altri problemi in Log Analytics, selezionare il tipo di problema appropriato in Log Analytics. |
| Problemi con le app M365 | Contattare l'interfaccia di amministrazione di M365 con una delle opzioni della Guida dell'interfaccia di [amministrazione di M365](/microsoft-365/admin/contact-support-for-business-products/). |

## <a name="next-steps"></a>Passaggi successivi

- Per risolvere i problemi durante la creazione di un pool host in un ambiente desktop virtuale Windows, vedere [creazione di pool host](troubleshoot-set-up-issues.md).
- Per risolvere i problemi durante la configurazione di una macchina virtuale (VM) in Desktop virtuale Windows, consultare [Configurazione di macchine virtuali nell'host sessione](troubleshoot-vm-configuration.md).
- Per risolvere i problemi relativi alle connessioni client di desktop virtuali Windows, vedere [connessioni al servizio desktop virtuale di Windows](troubleshoot-service-connection.md).
- Per risolvere i problemi relativi ai client di Desktop remoto, vedere [risoluzione dei problemi del client di desktop remoto](troubleshoot-client.md)
- Per risolvere i problemi relativi all'uso di PowerShell con Desktop virtuale di Windows, consultare [PowerShell con Desktop virtuale Windows](troubleshoot-powershell.md).
- Per ulteriori informazioni sul servizio, vedere [ambiente desktop virtuale di Windows](environment-setup.md).
- Per eseguire un'esercitazione di risoluzione dei problemi, vedere [Esercitazione: Risolvere i problemi delle distribuzioni dei modelli di Resource Manager](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Per altre informazioni sulle azioni di controllo, vedere [Operazioni di controllo con Resource Manager](../azure-resource-manager/management/view-activity-logs.md).
- Per informazioni sulle azioni per determinare gli errori durante la distribuzione, vedere [visualizzare le operazioni di distribuzione](../azure-resource-manager/templates/deployment-history.md).
