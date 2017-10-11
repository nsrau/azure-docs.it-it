---
title: Risolvere i problemi del proxy dell'applicazione | Documentazione Microsoft
description: Illustra come risolvere gli errori nel Proxy applicazione di Azure AD.
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 970caafb-40b8-483c-bb46-c8b032a4fb74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: H1Hack27Feb2017; it-pro
ms.openlocfilehash: 3ef74c8d5d5b75eb2bca4e6c994f4718b5226db8
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2017
---
# <a name="troubleshoot-application-proxy-problems-and-error-messages"></a>Risolvere i problemi e i messaggi di errore del proxy dell'applicazione
Se si verificano errori durante l'accesso a un'applicazione pubblicata o durante la pubblicazione di applicazioni, controllare le opzioni seguenti per verificare se il Proxy applicazione di Microsoft Azure AD funziona correttamente: 

* Aprire la console dei servizi Windows e verificare che il servizio **Microsoft AAD Application Proxy Connector** sia abilitato e in esecuzione. È anche possibile osservare la pagina delle proprietà del servizio Proxy applicazione, come mostrato nell'immagine seguente:   
  ![Schermata della finestra delle proprietà del connettore Proxy applicazione di Microsoft AAD](./media/active-directory-application-proxy-troubleshoot/connectorproperties.png)
* Aprire il Visualizzatore eventi e cercare gli eventi correlati al connettore del proxy di applicazione in **Registri applicazioni e servizi** > **Microsoft** > **AadApplicationProxy** > **Connector** > **Admin**.
* Se necessario, [attivando i log di sessione dei connettore proxy di applicazione](application-proxy-understand-connectors.md#under-the-hood) sono disponibili log più dettagliati.

Per altre informazioni sullo strumento di risoluzione dei problemi di Azure AD, vedere [Troubleshooting tool to validate connector networking prerequisites](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/03/troubleshooting-tool-to-validate-connector-networking-prerequisites) (Strumento di risoluzione dei problemi per convalidare i prerequisiti di rete del connettore).

## <a name="the-page-is-not-rendered-correctly"></a>La pagina non viene visualizzata correttamente
È possibile che si verifichino problemi di rendering o di funzionamento dell'applicazione, ma che non vengano visualizzati messaggi di errore specifici. Ciò può verificarsi se è stato pubblicato il percorso dell'articolo, ma l'applicazione richiede contenuto esistente al di fuori di tale percorso.

Se ad esempio si pubblica il percorso https://yourapp/app, ma l'applicazione chiama le immagini in https://yourapp/media, il rendering di queste ultime non verrà eseguito. Assicurarsi di pubblicare l'applicazione usando il percorso di livello più alto necessario per includere tutto il contenuto rilevante. In questo esempio il percorso sarebbe http://yourapp/.

Se si modifica il percorso per includere il contenuto a cui viene fatto riferimento, ma è comunque necessario che gli utenti possano accedere a un collegamento più diretto del percorso, vedere il post di blog [Setting the right link for Application Proxy Applications in the Azure AD access panel and Office 365 app launcher](https://blogs.technet.microsoft.com/applicationproxyblog/2016/04/06/setting-the-right-link-for-application-proxy-applications-in-the-azure-ad-access-panel-and-office-365-app-launcher/)(Impostazione del collegamento corretto per le applicazioni del proxy di applicazione nel pannello di accesso di Azure AD e nell'icona di avvio delle app di Office 365).

## <a name="connector-errors"></a>Errori del connettore

Usare lo [strumento per il test delle porte del connettore Proxy di applicazione Azure AD](https://aadap-portcheck.connectorporttest.msappproxy.net/) per verificare che il connettore possa raggiungere il servizio proxy di applicazione. Assicurarsi almeno che l'area Stati Uniti centrali e l'area più vicina all'utente abbiano segni di spunta verdi. La presenza di più segni di spunta verdi indica una maggiore resilienza. 

Se la registrazione non riesce durante l'installazione guidata del connettore, esistono due modi per visualizzare il motivo dell'errore. Cercare nel log eventi in **Registri applicazioni e servizi\Microsoft\AadApplicationProxy\Connector\Admin** oppure eseguire il comando di Windows PowerShell seguente:

    Get-EventLog application –source “Microsoft AAD Application Proxy Connector” –EntryType “Error” –Newest 1

Dopo aver individuato l'errore del connettore nel log eventi, usare questa tabella di errori comuni per risolvere il problema:

| Errore | Procedure consigliate |
| ----- | ----------------- |
| La registrazione del connettore non è riuscita: assicurarsi di avere abilitato il Proxy applicazione nel portale di gestione di Azure e di avere immesso il nome utente e la password di Active Directory corretti. Errore: "Si sono verificati uno o più errori". | Se si è chiusa la finestra di registrazione senza aver eseguito l'accesso ad Azure AD, eseguire di nuovo la creazione guidata del connettore e registrarlo. <br><br> Se la finestra di registrazione si apre e poi si chiude immediatamente senza consentire all'utente di accedere, è probabile che venga visualizzato questo errore. L'errore si verifica quando viene rilevato un errore di rete nel sistema. Assicurarsi che sia possibile connettersi da un browser a un sito Web pubblico e che le porte siano aperte come specificato in [Prerequisiti del Proxy dell’applicazione](active-directory-application-proxy-enable.md). |
| Viene visualizzato un errore di cancellazione nella finestra di registrazione. Impossibile proseguire | Se viene visualizzato questo errore e la finestra si chiude, è stato commesso un errore durante l'immissione di nome utente o password. Riprovare. |
| La registrazione del connettore non è riuscita: assicurarsi di avere abilitato il Proxy applicazione nel portale di gestione di Azure e di avere immesso il nome utente e la password di Active Directory corretti. Errore: "AADSTS50059: non sono state trovate informazioni di identificazione del tenant nella richiesta o incluse in modo implicito nelle credenziali fornite e la ricerca in base all'URI dell'entità servizio non è riuscita". | Si sta provando ad accedere con un account Microsoft e non con un dominio che fa parte dell'ID organizzazione della directory a cui si vuole accedere. Assicurarsi che l'amministratore faccia parte dello stesso nome di dominio del dominio tenant. Ad esempio, se il dominio di Azure AD è contoso.com, l'amministratore dovrà essere admin@contoso.com. |
| Non è possibile recuperare i criteri di esecuzione correnti per l'esecuzione degli script PowerShell. | Se l'installazione del connettore non riesce, verificare che il criterio di esecuzione di PowerShell non sia disabilitato. <br><br>1. Aprire l'Editor Criteri di gruppo.<br>2. Passare a **Configurazione computer** > **Modelli amministrativi** > **Componenti di Windows** > **Windows PowerShell** e fare doppio clic su **Attiva l'esecuzione di script**.<br>3. L'esecuzione di questo criterio può essere impostata su **Non configurato** o **Abilitato**. Se l'impostazione è **Abilitato**, verificare che in Opzioni la voce Criteri di esecuzione sia impostata su **Consenti script locali e script remoti firmati** o su **Consenti tutti gli script**. |
| Non è stato possibile configurare il connettore. | Il certificato client del connettore usato per l'autenticazione è scaduto. Questo errore può essere visualizzato anche se l'installazione del connettore è protetta da un proxy. In questo caso il connettore non può accedere a Internet e non riuscirà a fornire applicazioni agli utenti remoti. Rinnovare manualmente l'attendibilità con il cmdlet `Register-AppProxyConnector` in Windows PowerShell. Se il connettore è protetto da un proxy, è necessario concedere l'accesso a Internet agli account del connettore "servizi di rete" e "sistema locale". A questo scopo, è possibile concedere agli account l'accesso al proxy o impostarli perché ignorino il proxy. |
| La registrazione del connettore non è riuscita: per registrare il connettore, è necessario essere un amministratore globale di Active Directory. Errore: "La richiesta di registrazione è stata negata". | L'alias con cui si sta provando ad accedere non è un amministratore nel dominio. Il connettore viene sempre installato per la directory a cui appartiene il dominio dell'utente. Assicurarsi che l'account amministratore con cui si sta provando ad accedere disponga delle autorizzazioni globali per il tenant di Azure AD. |

## <a name="kerberos-errors"></a>Errori di Kerberos

Questa tabella contiene gli errori più comuni generati dall'installazione e dalla configurazione di Kerberos e include suggerimenti per la risoluzione.

| Errore | Procedure consigliate |
| ----- | ----------------- |
| Non è possibile recuperare i criteri di esecuzione correnti per l'esecuzione degli script PowerShell. | Se l'installazione del connettore non riesce, verificare che il criterio di esecuzione di PowerShell non sia disabilitato.<br><br>1. Aprire l'Editor Criteri di gruppo.<br>2. Passare a **Configurazione computer** > **Modelli amministrativi** > **Componenti di Windows** > **Windows PowerShell** e fare doppio clic su **Attiva l'esecuzione di script**.<br>3. L'esecuzione di questo criterio può essere impostata su **Non configurato** o **Abilitato**. Se l'impostazione è **Abilitato**, verificare che in Opzioni la voce Criteri di esecuzione sia impostata su **Consenti script locali e script remoti firmati** o su **Consenti tutti gli script**. |
| 12008: Azure AD ha superato il numero massimo di tentativi di autenticazione Kerberos consentiti al server back-end. | Questo errore può indicare una configurazione non corretta tra Azure AD e il server back-end dell'applicazione oppure un problema di configurazione di data e ora su entrambi i computer. Il server back-end ha rifiutato il ticket Kerberos creato da Azure AD. Verificare che Azure AD e il server applicazioni back-end siano configurati correttamente. Assicurarsi che la configurazione di data e ora in Azure AD e nel server back-end dell'applicazione siano sincronizzate. |
| 13016: Azure AD non riesce a recuperare un ticket Kerberos per conto dell'utente, perché non esiste alcun UPN nel token perimetrale o nel cookie di accesso. | Si è verificato un problema con la configurazione del servizio token di sicurezza. Correggere la configurazione dell'attestazione UPN nel servizio token di sicurezza. |
| 13019: Azure AD non riesce a recuperare un ticket Kerberos per conto dell'utente a causa dell'errore generale dell'API seguente. | Questo evento può indicare una configurazione non corretta tra Azure AD e il server controller di dominio oppure un problema di configurazione di data e ora su entrambi i computer. Il controller di dominio ha rifiutato il ticket Kerberos creato da Azure AD. Verificare che Azure AD e il server applicazioni back-end siano configurati correttamente, in particolare la configurazione del nome dell'entità servizio. Verificare che Azure AD sia aggiunto allo stesso dominio del controller di dominio, per assicurarsi che il controller di dominio stabilisca relazioni di trust con Azure AD. Assicurarsi che la configurazione di data e ora in Azure AD e nel controller di dominio dell'applicazione siano sincronizzate. |
| 13020: Azure AD non riesce a recuperare un ticket Kerberos per conto dell'utente, perché il nome dell'entità servizio del server back-end non è definito. | Questo evento può indicare una configurazione non corretta tra Azure AD e il server controller di dominio oppure un problema di configurazione di data e ora su entrambi i computer. Il controller di dominio ha rifiutato il ticket Kerberos creato da Azure AD. Verificare che Azure AD e il server applicazioni back-end siano configurati correttamente, in particolare la configurazione del nome dell'entità servizio. Verificare che Azure AD sia aggiunto allo stesso dominio del controller di dominio, per assicurarsi che il controller di dominio stabilisca relazioni di trust con Azure AD. Assicurarsi che la configurazione di data e ora in Azure AD e nel controller di dominio dell'applicazione siano sincronizzate. |
| 13022: Azure AD non riesce ad autenticare l'utente perché il server back-end risponde ai tentativi di autenticazione Kerberos con un errore HTTP 401. | Questo evento può indicare una configurazione non corretta tra Azure AD e il server back-end dell'applicazione oppure un problema di configurazione di data e ora su entrambi i computer. Il server back-end ha rifiutato il ticket Kerberos creato da Azure AD. Verificare che Azure AD e il server applicazioni back-end siano configurati correttamente. Assicurarsi che la configurazione di data e ora in Azure AD e nel server back-end dell'applicazione siano sincronizzate. |

## <a name="end-user-errors"></a>Errori utente finale

Questo elenco contiene gli errori che potrebbero verificarsi quando gli utenti finali tentano di accedere all'app senza riuscirci. 

| Errore | Procedure consigliate |
| ----- | ----------------- |
| Il sito Web non riesce a visualizzare la pagina. | L'utente può visualizzare questo errore quando prova ad accedere all'app pubblicata, se l'applicazione è un'applicazione con autenticazione integrata di Windows. Il nome dell'entità servizio dell'applicazione definito potrebbe non essere corretto. Per le app con autenticazione integrata di Windows, assicurarsi che il nome dell'entità servizio configurato per l'applicazione sia corretto. |
| Il sito Web non riesce a visualizzare la pagina. | L'utente può visualizzare questo errore quando prova ad accedere all'app pubblicata, se si tratta di un'applicazione OWA. Il problema può dipendere da uno dei motivi seguenti: <br><li>Il nome dell'entità servizio dell'applicazione definito non è corretto. Assicurarsi che il nome dell'entità servizio configurato per l'applicazione sia corretto.</li><li>L'utente che ha provato ad accedere all'applicazione sta usando un account Microsoft invece dell'account aziendale appropriato oppure si tratta di un utente guest. Assicurarsi che l'utente acceda con il proprio account aziendale corrispondente al dominio dell'applicazione pubblicata. Gli utenti con account Microsoft o guest non possono accedere alle applicazioni con autenticazione integrata di Windows.</li><li>L'utente che ha provato ad accedere all'applicazione non è definito correttamente per l'applicazione sul lato locale. Assicurarsi che l'utente abbia le autorizzazioni appropriate definite per questa applicazione back-end nel computer locale. |
| Non è possibile accedere a questa app aziendale. L'utente non è autorizzato ad accedere a questa applicazione. Autorizzazione non riuscita. Assicurarsi di assegnare all'utente l'accesso a questa applicazione. | Gli utenti possono visualizzare questo errore quando provano ad accedere all'app pubblicata usando un account Microsoft invece del proprio account aziendale. Anche gli utenti guest possono visualizzare questo errore. Gli utenti con account Microsoft o guest non possono accedere alle applicazioni con autenticazione integrata di Windows. Assicurarsi che l'utente acceda con il proprio account aziendale corrispondente al dominio dell'applicazione pubblicata.<br><br>L'utente potrebbe non essere stato assegnato per l'applicazione. Passare alla scheda **Applicazione**, quindi in **Utenti e gruppi** assegnare l'utente o il gruppo di utenti all'applicazione. |
| Non è possibile accedere a questa app aziendale in questo momento. Riprovare più tardi. Timeout del connettore. | Gli utenti possono visualizzare questo errore quando provano ad accedere all'app pubblicata, se non sono definiti correttamente per questa applicazione sul lato locale. Assicurarsi che l'utente abbia le autorizzazioni appropriate definite per questa applicazione back-end nel computer locale. |
| Non è possibile accedere a questa app aziendale. L'utente non è autorizzato ad accedere a questa applicazione. Autorizzazione non riuscita. Accertarsi che l'utente abbia una licenza per Azure Active Directory Premium o Basic. | Questo errore potrebbe essere visualizzato dall'utente quando prova ad accedere all'app pubblicata, se non gli è stata esplicitamente assegnata una licenza Premium/Basic dall'amministratore del sottoscrittore. Accedere alla scheda **Licenze** di Active Directory del sottoscrittore e verificare che all'utente o al gruppo di utenti sia stata assegnata una licenza Premium o Basic. |

## <a name="my-error-wasnt-listed-here"></a>L'errore non è riportato in questi elenchi

Se si verifica un errore o un problema con il Proxy dell'applicazione Azure AD che non è elencato in questa guida alla risoluzione dei problemi, è necessario segnalarlo. Invia un'email al [team che si occupa del feedback](mailto:aadapfeedback@microsoft.com) specificando i dettagli dell'errore che si è verificato.

## <a name="see-also"></a>Vedere anche
* [Abilitare il proxy di applicazione per Azure Active Directory](active-directory-application-proxy-enable.md)
* [Pubblicare le applicazioni con il proxy di applicazione](active-directory-application-proxy-publish.md)
* [Abilita Single Sign-On](active-directory-application-proxy-sso-using-kcd.md)
* [Abilitare l'accesso condizionale](active-directory-application-proxy-conditional-access.md)


<!--Image references-->
[1]: ./media/active-directory-application-proxy-troubleshoot/connectorproperties.png
[2]: ./media/active-directory-application-proxy-troubleshoot/sessionlog.png
