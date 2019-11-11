---
title: Windows Virtual Desktop Management Tool-Azure
description: Come risolvere i problemi relativi allo strumento di gestione dei desktop virtuali di Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 11/09/2019
ms.author: helohr
ms.openlocfilehash: dd9fd135536fc2cb222b908f3d61c1d1430e65b0
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2019
ms.locfileid: "73905615"
---
# <a name="troubleshoot-the-windows-virtual-desktop-management-tool"></a>Risolvere i problemi relativi allo strumento di gestione di desktop virtuali Windows

Questo articolo descrive i problemi che possono verificarsi durante la distribuzione dello strumento di gestione del desktop virtuale di Windows e come risolverli.

## <a name="error-management-tool-services-configured-but-automated-setup-fails"></a>Errore: configurazione dei servizi degli strumenti di gestione non riuscita

Quando si configurano correttamente i servizi per lo strumento di gestione, ma l'installazione automatica non riesce, verrà visualizzato questo messaggio di errore:

```console
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n "status": "Failed",\r\n "error": {\r\n "code": "ResourceDeploymentFailure",\r\n "message": "The resource operation completed with terminal provisioning state 'Failed'."\r\n }\r\n}"}]}
```

Ciò significa in genere uno dei due elementi seguenti:

- L'utente dispone delle autorizzazioni di proprietario per la sottoscrizione e l'amministratore globale a livello del tenant, ma non può accedere ad Azure.
- Per le impostazioni dell'account utente è abilitata l'autenticazione a più fattori.

Per risolvere il problema:

1. Assicurarsi che l'utente creato per il Azure Active Directory nome dell'entità utente abbia il livello di sottoscrizione "collaboratore".
2. Accedere a < Portal. Azure. com > con l'account UPN per verificare le impostazioni dell'account e assicurarsi che la funzionalità autenticazione a più fattori non sia attiva. Se è attivata, disattivarla.
3. Visitare la pagina di consenso per desktop virtuali Windows e verificare che le app Server e client dispongano del consenso.
4. Esaminare l'esercitazione [distribuire uno strumento di gestione](manage-resources-using-ui.md) se il problema persiste e ridistribuire lo strumento.

## <a name="error-job-with-specified-id-already-exists"></a>Errore: processo con ID specificato già esistente

Se l'utente Visualizza il messaggio di errore "il processo con l'ID specificato esiste già", è perché non ha fornito un nome univoco nel parametro "Application Name" durante la distribuzione del modello.

Per risolvere questo problema, ridistribuire lo strumento di gestione con il parametro "Application Name" compilato.

## <a name="delayed-consent-prompt-when-opening-management-tool"></a>Richiesta di consenso ritardato durante l'apertura dello strumento di gestione

Quando si distribuisce lo strumento di gestione, è possibile che la richiesta di consenso non venga aperta immediatamente. Questo significa che il caricamento del servizio app Web di Azure richiede più tempo del solito. Il prompt verrà visualizzato al termine del caricamento del Web di Azure.

## <a name="the-user-cant-deploy-the-management-tool-in-the-east-us-region"></a>L'utente non può distribuire lo strumento di gestione nell'area Stati Uniti orientali

Se un cliente imposta l'area su Stati Uniti orientali, non potrà distribuire lo strumento di gestione.

Per risolvere questo problema, distribuire lo strumento di gestione in un'area diversa. La ridistribuzione dello strumento in un'area diversa non dovrebbe influire sull'esperienza utente.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulle tracce di escalation in [panoramica sulla risoluzione dei problemi, commenti e suggerimenti e supporto](troubleshoot-set-up-overview.md).
- Informazioni su come segnalare i problemi con gli strumenti per desktop virtuali di Windows nei [modelli ARM per Servizi Desktop remoto](https://github.com/Azure/RDS-Templates/blob/master/README.md).
- Per una panoramica sulla risoluzione dei problemi relativi a desktop virtuale Windows e alle tracce di escalation, vedere [panoramica sulla risoluzione dei problemi, commenti e suggerimenti e supporto](troubleshoot-set-up-overview.md).
- Per informazioni su come distribuire lo strumento di gestione, vedere [deploy an Management Tool](manage-resources-using-ui.md).