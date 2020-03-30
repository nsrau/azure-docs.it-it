---
title: Strumento di gestione di Windows Virtual Desktop - Azure
description: Risoluzione dei problemi relativi allo strumento di gestione di Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 11/09/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9d07ba42e83d9eec071ab047e9e1e92bac1f1411
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127496"
---
# <a name="troubleshoot-the-windows-virtual-desktop-management-tool"></a>Risolvere i problemi relativi allo strumento di gestione di Desktop virtuale Windows

In questo articolo vengono descritti i problemi che possono verificarsi durante la distribuzione dello strumento di gestione di Windows Virtual Desktop e viene illustrato come risolverli.

## <a name="error-management-tool-services-configured-but-automated-setup-fails"></a>Errore: servizi dello strumento di gestione configurati ma l'installazione automatica non riesce

Quando si configurano correttamente i servizi per lo strumento di gestione, ma l'installazione automatica non riesce, verrà visualizzato questo messaggio di errore:

```console
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n "status": "Failed",\r\n "error": {\r\n "code": "ResourceDeploymentFailure",\r\n "message": "The resource operation completed with terminal provisioning state 'Failed'."\r\n }\r\n}"}]}
```

Questo di solito significa una delle seguenti due cose:

- L'utente dispone delle autorizzazioni di proprietario per la sottoscrizione e dell'amministratore globale a livello di tenant, ma non può accedere ad Azure.The user has owner permissions on their subscription and global admin at tenant level, but't can sign in to Azure.
- Le impostazioni dell'account dell'utente hanno l'autenticazione a più fattori abilitata.

Per risolvere il problema:

1. Assicurarsi che l'utente creato per il nome dell'entità utente di Azure Active Directory abbia il livello di sottoscrizione "Collaboratore".
2. Accedi a <portal.azure.com> con l'account UPN per controllare le impostazioni dell'account e assicurarti che l'autenticazione a più fattori non sia attivata. Se è acceso, disattivalo.
3. Visita la pagina Consenso di Windows Virtual Desktop e assicurati che le app server e client abbiano il consenso.
4. Esaminare l'esercitazione [Distribuire uno strumento](manage-resources-using-ui.md) di gestione se il problema persiste e ridistribuire lo strumento.

## <a name="error-job-with-specified-id-already-exists"></a>Errore: il processo con l'ID specificato esiste già

Se l'utente visualizza il messaggio di errore "Il processo con l'ID specificato esiste già", è perché non ha fornito un nome univoco nel parametro "Nome applicazione" durante la distribuzione del modello.

Per risolvere il problema, ridistribuire lo strumento di gestione con il parametro "Nome applicazione" compilato.

## <a name="delayed-consent-prompt-when-opening-management-tool"></a>Richiesta di consenso ritardato all'apertura dello strumento di gestione

Quando si distribuisce lo strumento di gestione, la richiesta di consenso potrebbe non aprirsi immediatamente. Ciò significa che il caricamento del servizio app Web di Azure richiede più tempo del solito. Il messaggio di richiesta dovrebbe essere visualizzato al termine del caricamento del Web di Azure.The prompt should appear after Azure Web is done loading.

## <a name="the-user-cant-deploy-the-management-tool-in-the-east-us-region"></a>L'utente non può distribuire lo strumento di gestione nell'area Stati Uniti orientali

Se un cliente imposta l'area su Stati Uniti orientali, non può distribuire lo strumento di gestione.

Per risolvere il problema, distribuire lo strumento di gestione in un'area diversa. La ridistribuzione dello strumento in un'area diversa non dovrebbe influire sull'esperienza utente.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulle tracce di riassegnazione, [vedere Panoramica, feedback e supporto per la risoluzione dei problemi.](troubleshoot-set-up-overview.md)
- Per informazioni su come segnalare problemi con gli strumenti di Windows Virtual Desktop, vedere [Modelli ARM per Servizi Desktop remoto.](https://github.com/Azure/RDS-Templates/blob/master/README.md)
- Per una panoramica sulla risoluzione dei problemi di Desktop virtuale di Windows e sulle tracce di escalation, vedere [Panoramica della risoluzione dei problemi, feedback e supporto.](troubleshoot-set-up-overview.md)
- Per informazioni su come distribuire lo strumento di gestione, vedere Distribuire uno strumento di [gestione.](manage-resources-using-ui.md)