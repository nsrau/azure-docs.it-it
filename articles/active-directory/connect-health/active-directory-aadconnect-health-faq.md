---
title: Domande frequenti su Azure AD Connect Health
description: "Di seguito sono elencate le domande frequenti e le relative risposte su Azure AD Connect Health. In questa sezione sono contenute le domande sull&quot;uso del servizio, inclusi il modello di fatturazione, le funzionalità, le limitazioni e il supporto."
services: active-directory
documentationcenter: 
author: billmath
manager: samueld
editor: curtand
ms.assetid: f1b851aa-54d7-4cb4-8f5c-60680e2ce866
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: vakarand
translationtype: Human Translation
ms.sourcegitcommit: 2f990f4c888da91422a6a6e9873307cbb483e782
ms.openlocfilehash: b6687bc0ccde0e257338a939f39e343f75a6f5df


---
# <a name="azure-ad-connect-health-frequently-asked-questions-faq"></a>Domande frequenti su Azure AD Connect Health
Di seguito sono elencate le domande frequenti e le relative risposte su Azure AD Connect Health. In questa sezione sono contenute le domande sull'uso del servizio, inclusi il modello di fatturazione, le funzionalità, le limitazioni e il supporto.

## <a name="general-questions"></a>Domande generali
**D: Quando si gestiscono più directory di Azure AD, come è possibile passare a quella con Azure Active Directory Premium?**

È possibile spostarsi tra tenant di Azure AD diversi selezionando il nome dell'utente connesso nell'angolo in alto a destra e scegliendo l'account appropriato. Se l'account non è incluso nell'elenco, disconnettersi e quindi usare le credenziali di amministratore globale della directory con Azure Active Directory Premium abilitato per l'accesso.

## <a name="installation-questions"></a>Domande sull'installazione
**D: Qual è l'impatto dell'installazione dell'agente di Azure AD Connect Health in singoli server?**

L'impatto dell'installazione degli agenti di Microsoft Azure AD Connect Health AD FS, dei server proxy applicazione Web, dei server Azure AD Connect (sincronizzazione) e dei controller di dominio è minimo per quanto riguarda CPU, utilizzo della memoria, larghezza di banda della rete e archiviazione.

I numeri indicati di seguito sono approssimativi.

* Utilizzo della CPU: ~1% di aumento
* Utilizzo della memoria: fino a al 10% della memoria di sistema totale

> [!NOTE]
> Nel caso in cui l'agente non sia in grado di comunicare con Azure, l'agente archivia i dati in locale, fino a un limite massimo definito. L'agente sovrascrive i dati "memorizzati nella cache" secondo un criterio di tipo "intervento di manutenzione meno recente".
> 
> 

* Archiviazione buffer locale per gli agenti di Azure AD Connect Health: circa 20 MB
* Per i server AD FS è consigliabile eseguire il provisioning di 1024 MB (1 GB) di spazio su disco per il canale di controllo di AD FS per consentire agli agenti di Azure AD Connect Health di elaborare tutti i dati di controllo prima che vengano sovrascritti.

**D: È necessario riavviare i server durante l'installazione degli agenti di Azure AD Connect Health?**

No. Per l'installazione degli agenti non è necessario il riavvio del server. Tuttavia, l'installazione di alcuni passaggi preliminari necessari può richiedere un riavvio del server.

Ad esempio, in Windows Server 2008 R2 l'installazione di .Net 4.5 Framework richiede un riavvio del server.

**Q: Il servizio Azure AD Connect Health funziona tramite un proxy HTTP pass-through?**

Sì.  Per le operazioni in corso è possibile configurare l'agente di Health per l'inoltro delle richieste HTTP in uscita tramite un proxy HTTP.

Se è necessario configurare un proxy durante la registrazione dell'agente, modificare prima le impostazioni del proxy di Internet Explorer.

1. Aprire Internet Explorer -> Impostazioni -> Opzioni Internet -> Connessioni -> Impostazioni LAN.
2. Selezionare Usa un server di proxy per la rete LAN.
3. Selezionare Avanzate se sono presenti porte proxy diverse per HTTP e HTTPS/Protetto.

**D: I servizi di Azure AD Connect Health supportano l'autenticazione di base durante la connessione a proxy HTTP?**

No. Non è attualmente supportato alcun meccanismo per specificare in modo arbitrario un nome utente o una password per l'autenticazione di base.

**D: Quali versioni di Servizi di dominio Active Directory sono supportate da Azure AD Connect Health per Servizi di dominio Active Directory?**

Il monitoraggio di Servizi di dominio Active Directory è supportato durante l'installazione nelle versioni del sistema operativo seguenti:

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

## <a name="operations-questions"></a>Domande sulle operazioni
**Q: È necessario abilitare il controllo nei server proxy applicazione ADFS o nei server proxy applicazione Web?**

No, il controllo non deve essere abilitato nei server proxy applicazione Web. Abilitarlo nei server AD FS.

**D: In che modo vengono risolti gli avvisi di Azure AD Connect Health?**

Gli avvisi di Azure AD Connect Health vengono risolti se si verifica una condizione di esito positivo. Gli agenti di Azure AD Connect Health rilevano e segnalano periodicamente al servizio le condizioni di esito positivo. Per alcuni avvisi, l'eliminazione è basata sul tempo. In altri termini, se entro 72 ore dalla generazione dell'avviso non viene osservata la stessa condizione di errore, l'avviso viene automaticamente risolto.

**D: Quali porte del firewall è necessario aprire per garantire il funzionamento dell'agente di Azure AD Connect Health?**

È necessario che siano aperte le porte TCP/UDP 443 e 5671 per consentire all'agente di Azure AD Connect Health di comunicare con gli endpoint del servizio Azure AD Connect Health.

**D: Perché vengono visualizzati due server con lo stesso nome nel portale di Azure AD Connect Health?**

Quando si rimuove un agente da un server, tale server non viene automaticamente rimosso dal portale di Azure AD Connect.  Se è stato rimosso manualmente un agente da un server o se è stato rimosso il server stesso, è necessario eliminare manualmente la voce relativa al server dal portale di Azure AD Connect Health. 

Se è stata creata una nuova immagine di un server o se è stato creato un nuovo server con gli stessi dettagli, ad esempio il nome del computer, e il server già registrato non è stato rimosso dal portale di Azure AD Connect Health, una volta installato l'agente nel nuovo server è possibile che vengano visualizzate due voci con lo stesso nome.  
In questo caso è consigliabile eliminare manualmente la voce appartenente al server precedente. I dati per questo server non dovrebbero essere aggiornati.

## <a name="related-links"></a>Collegamenti correlati
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Installazione dell'agente di Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md)
* [Operazioni di Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
* [Uso di Azure AD Connect Health con AD FS](active-directory-aadconnect-health-adfs.md)
* [Uso di Azure AD Connect Health per la sincronizzazione](active-directory-aadconnect-health-sync.md)
* [Uso di Azure AD Connect Health con Servizi di dominio Active Directory](active-directory-aadconnect-health-adds.md)
* [Cronologia delle versioni di Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)




<!--HONumber=Jan17_HO3-->


