---
title: Risolvere i problemi di accesso al registro di sistema
description: Sintomi, cause e risoluzione dei problemi comuni durante l'accesso a un registro contenitori di Azure
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: f7bac49a79d32af3a0e533f4c4e3431c62b82172
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92148446"
---
# <a name="troubleshoot-registry-login"></a>Risolvere i problemi di accesso al registro

Questo articolo consente di risolvere i problemi che possono verificarsi durante l'accesso a un registro contenitori di Azure. 

## <a name="symptoms"></a>Sintomi

Può includere uno o più degli elementi seguenti:

* Non è possibile accedere al registro di sistema usando `docker login` , `az acr login` o entrambi
* Non è possibile accedere al registro di sistema e viene visualizzato l'errore `unauthorized: authentication required` o `unauthorized: Application not registered with AAD`
* Non è possibile accedere al registro di sistema e si riceve un errore dell'interfaccia della riga `Could not connect to the registry login server`
* Non è possibile eseguire il push o il pull delle immagini e si riceve un errore di Docker `unauthorized: authentication required`
* Non è possibile accedere al registro di sistema dal servizio Azure Kubernetes, Azure DevOps o un altro servizio di Azure
* Non è possibile accedere al registro di sistema e viene visualizzato un errore `Error response from daemon: login attempt failed with status: 403 Forbidden` . vedere [risolvere i problemi di rete con il registro](container-registry-troubleshoot-access.md)
* Non è possibile accedere alle impostazioni del registro di sistema o visualizzarle in portale di Azure o gestire il registro di sistema con l'interfaccia

## <a name="causes"></a>Cause

* Docker non è configurato correttamente nell'ambiente- [soluzione](#check-docker-configuration)
* Il registro di sistema non esiste o il nome non è corretto- [soluzione](#specify-correct-registry-name)
* Le credenziali del registro di sistema non sono valide- [soluzione](#confirm-credentials-to-access-registry)
* Le credenziali non sono autorizzate per le operazioni Push, pull o Azure Resource Manager- [soluzione](#confirm-credentials-are-authorized-to-access-registry)
* Le credenziali sono scadute- [soluzione](#check-that-credentials-arent-expired)

## <a name="further-diagnosis"></a>Ulteriore diagnosi 

Eseguire il comando [AZ ACR check-Health](/cli/azure/acr#az-acr-check-health) per ottenere altre informazioni sull'integrità dell'ambiente del registro di sistema e, facoltativamente, l'accesso a un registro di sistema di destinazione. Ad esempio, diagnosticare gli errori di configurazione di Docker o Azure Active Directory problemi di accesso. 

Vedere [verificare l'integrità di un registro contenitori di Azure](container-registry-check-health.md) per gli esempi di comando. Se vengono segnalati errori, esaminare il [riferimento all'errore](container-registry-health-error-reference.md) e le sezioni seguenti per le soluzioni consigliate.

> [!NOTE]
> Alcuni errori di autenticazione o autorizzazione possono verificarsi anche se sono presenti configurazioni di rete o del firewall che impediscono l'accesso al registro di sistema. Vedere [risolvere i problemi di rete con il registro di sistema](container-registry-troubleshoot-access.md).

## <a name="potential-solutions"></a>Possibili soluzioni

### <a name="check-docker-configuration"></a>Controllare la configurazione di Docker

Per la maggior parte dei flussi di autenticazione di Azure Container Registry è necessaria un'installazione locale di Docker, in modo da poter eseguire l'autenticazione con il registro per operazioni come il push e il pull delle immagini Verificare che il client e il daemon dell'interfaccia della riga di comando di Docker (motore Docker) siano in esecuzione nell'ambiente. È necessario il client Docker versione 18,03 o successiva.

Collegamenti correlati:

* [Panoramica dell'autenticazione](container-registry-authentication.md#authentication-options)
* [Domande frequenti sul registro contenitori](container-registry-faq.md)

### <a name="specify-correct-registry-name"></a>Specificare il nome del registro di sistema corretto

Quando si utilizza `docker login` , specificare il nome completo del server di accesso del registro di sistema, ad esempio *MyRegistry.azurecr.io*. Assicurarsi di usare solo lettere minuscole. Esempio:

```console
docker login myregistry.azurecr.io
```

Quando si usa [AZ ACR login](/cli/azure/acr#az-acr-login) con un'identità Azure Active Directory, [accedere](/cli/azure/authenticate-azure-cli)prima di tutto all'interfaccia della riga di comando di Azure e quindi specificare il nome della risorsa di Azure del registro di sistema. Il nome della risorsa è il nome fornito al momento della creazione del registro di sistema, ad esempio *Registro di sistema* (senza suffisso di dominio). Esempio:

```azurecli
az acr login --name myregistry
```

Collegamenti correlati:

* [az acr login ha esito positivo, ma i comandi di Docker hanno esito negativo con errore: non autorizzato: autenticazione obbligatoria](container-registry-faq.md#az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required )

### <a name="confirm-credentials-to-access-registry"></a>Confermare le credenziali per accedere al registro di sistema

Verificare la validità delle credenziali utilizzate per lo scenario o fornite dal proprietario del registro di sistema. Alcuni possibili problemi:

* Se si usa un'entità servizio Active Directory, assicurarsi di usare le credenziali corrette nel tenant di Active Directory:
  * Nome utente: ID dell'applicazione dell'entità servizio (noto anche come *ID client*)
  * Password: password dell'entità servizio (detta anche *segreto client*)
* Se si usa un servizio di Azure come Azure Kubernetes Service o Azure DevOps per accedere al registro di sistema, verificare la configurazione del registro di sistema per il servizio.
* Se è stato eseguito `az acr login` con l' `--expose-token` opzione, che Abilita l'accesso al registro di sistema senza usare il daemon Docker, assicurarsi di eseguire l'autenticazione con il nome utente `00000000-0000-0000-0000-000000000000` .

Collegamenti correlati:

* [Panoramica dell'autenticazione](container-registry-authentication.md#authentication-options)
* [Accesso individuale con Azure AD](container-registry-authentication.md#individual-login-with-azure-ad)
* [Accedi con entità servizio](container-registry-auth-service-principal.md)
* [Accedi con identità gestita](container-registry-authentication-managed-identity.md)
* [Accedi con token con ambito repository](container-registry-repository-scoped-permissions.md)
* [Accedi con account amministratore](container-registry-authentication.md#admin-account)
* [Azure AD codici di errore di autenticazione e autorizzazione](../active-directory/develop/reference-aadsts-error-codes.md)
* informazioni di riferimento su [AZ ACR login](/cli/azure/acr#az-acr-login)

### <a name="confirm-credentials-are-authorized-to-access-registry"></a>Verificare che le credenziali siano autorizzate ad accedere al registro

Confermare le autorizzazioni del registro di sistema associate alle credenziali, ad esempio il `AcrPull` ruolo Azure per eseguire il pull delle immagini dal registro di sistema o il `AcrPush` ruolo per eseguire il push delle immagini. 

Per accedere a un registro nel portale o alla gestione del registro di sistema con l'interfaccia della riga di comando di Azure, è necessario almeno il `Reader` ruolo per eseguire Azure Resource Manager operazioni.

Per aggiungere o rimuovere assegnazioni di ruolo, l'utente o il proprietario del registro di sistema deve disporre di privilegi sufficienti nella sottoscrizione.

Collegamenti correlati:

* [Ruoli e autorizzazioni di Azure-Azure Container Registry](container-registry-roles.md)
* [Accedi con token con ambito repository](container-registry-repository-scoped-permissions.md)
* [Aggiungere o rimuovere assegnazioni di ruolo di Azure usando il portale di Azure](../role-based-access-control/role-assignments-portal.md)
* [Usare il portale per creare un'applicazione Azure AD e un'entità servizio che possano accedere alle risorse](../active-directory/develop/howto-create-service-principal-portal.md)
* [Crea un nuovo segreto dell'applicazione](../active-directory/develop/howto-create-service-principal-portal.md#option-2-create-a-new-application-secret)
* [Azure AD codici di autenticazione e autorizzazione](../active-directory/develop/reference-aadsts-error-codes.md)

### <a name="check-that-credentials-arent-expired"></a>Verificare che le credenziali non siano scadute

I token e le credenziali Active Directory possono scadere dopo i periodi definiti, impedendo l'accesso al registro di sistema. Per abilitare l'accesso, potrebbe essere necessario reimpostare o rigenerare le credenziali.

* Se si usa un'identità di Active Directory singola, un'identità gestita o un'entità servizio per l'accesso al registro di sistema, il token AD scade dopo 3 ore. Accedere di nuovo al registro di sistema.  
* Se si usa un'entità servizio Active Directory con un segreto client scaduto, un proprietario della sottoscrizione o un amministratore dell'account deve reimpostare le credenziali o generare una nuova entità servizio.
* Se si usa un [token con ambito repository](container-registry-repository-scoped-permissions.md), un proprietario del registro di sistema potrebbe dover reimpostare una password o generare un nuovo token.

Collegamenti correlati:

* [Reimposta le credenziali dell'entità servizio](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset)
* [Rigenera password token](container-registry-repository-scoped-permissions.md#regenerate-token-passwords)
* [Accesso individuale con Azure AD](container-registry-authentication.md#individual-login-with-azure-ad)

## <a name="advanced-troubleshooting"></a>Risoluzione dei problemi avanzata

Se nel registro di sistema è abilitata la [raccolta di log delle risorse](container-registry-diagnostics-audit-logs.md) , esaminare il log ContainterRegistryLoginEvents. Questo log archivia gli eventi e lo stato di autenticazione, inclusi l'identità in ingresso e l'indirizzo IP. Eseguire una query sul log per individuare gli [errori di autenticazione del registro](container-registry-diagnostics-audit-logs.md#registry-authentication-failures). 

Collegamenti correlati:

* [Log per la valutazione diagnostica e il controllo](container-registry-diagnostics-audit-logs.md)
* [Domande frequenti sul registro contenitori](container-registry-faq.md)
* [Procedure consigliate per Registro Azure Container](container-registry-best-practices.md)

## <a name="next-steps"></a>Passaggi successivi

Se non si risolve il problema, vedere le opzioni seguenti.

* Altri argomenti sulla risoluzione dei problemi del registro di sistema includono:
  * [Risolvere i problemi di rete con il registro di sistema](container-registry-troubleshoot-access.md)
  * [Risolvere i problemi relativi alle prestazioni del registro](container-registry-troubleshoot-performance.md)
* Opzioni di [supporto della community](https://azure.microsoft.com/support/community/)
* [Domane e risposte Microsoft](/answers/products/)
* [Aprire un ticket di supporto](https://azure.microsoft.com/support/create-ticket/) in base alle informazioni fornite, è possibile che venga eseguita una diagnostica rapida per gli errori di autenticazione nel registro di sistema