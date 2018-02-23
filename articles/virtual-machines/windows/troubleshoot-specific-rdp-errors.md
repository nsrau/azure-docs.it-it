---
title: Specifici messaggi di errore RDP per VM di Azure | Documentazione Microsoft
description: Comprendere specifici messaggi di errore che possono essere visualizzati quando si tenta di usare una connessione Desktop remoto a una macchina virtuale Windows in Azure
keywords: Errore di desktop remoto, errore di connessione al desktop remoto, impossibile connettersi alla macchina virtuale, risoluzione dei problemi di desktop remoto
services: virtual-machines-windows
documentationcenter: 
author: genlin
manager: timlt
editor: 
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: 5feb1d64-ee6f-4907-949a-a7cffcbc6153
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 199f8c62b7aa3310bebd33c88edf57ea3bce804b
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2018
---
# <a name="troubleshooting-specific-rdp-error-messages-to-a-windows-vm-in-azure"></a>Risoluzione dei problemi relativi a specifici messaggi di errore RDP inviati a una VM Windows in Azure
Quando si usa una connessione Desktop remoto a una macchina virtuale (VM) Windows in Azure, è possibile ricevere uno specifico messaggio di errore. Questo articolo illustra nei dettagli alcuni dei più comuni messaggi di errore visualizzati e spiega le procedure per la risoluzione dei problemi relativi a tali messaggi. Se si verificano problemi di connessione alla VM mediante RDP ma non viene visualizzato un messaggio di errore specifico, vedere la [guida alla risoluzione dei problemi relativi a Desktop remoto](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Per informazioni su messaggi di errore specifici, vedere quanto segue:

* [La sessione remota è stata disconnessa perché non sono disponibili server licenze di Desktop remoto per il rilascio della licenza](#rdplicense).
* [Desktop remoto: impossibile rilevare il "nome" del computer](#rdpname).
* [Si è verificato un errore di autenticazione. Impossibile contattare l'autorità di sicurezza locale](#rdpauth).
* [Errore di sicurezza di Windows: Le credenziali specificate non funzionano](#wincred).
* [Il computer non è in grado di connettersi al computer remoto](#rdpconnect).

<a id="rdplicense"></a>

## <a name="the-remote-session-was-disconnected-because-there-are-no-remote-desktop-license-servers-available-to-provide-a-license"></a>La sessione remota è stata disconnessa perché non sono disponibili server licenze di Desktop remoto per il rilascio della licenza.
Causa: il periodo di prova di 120 giorni delle licenza per il ruolo Server Desktop remoto è scaduto ed è necessario installare le licenze.

Per risolvere il problema, salvare una copia locale del file RDP dal portale e al prompt dei comandi di PowerShell eseguire questo comando per avviare la connessione. Questo passaggio disabilita la licenza solo per la connessione in oggetto:

        mstsc <File name>.RDP /admin

Se in realtà non sono necessarie più di due connessioni di desktop remoto contemporanee nella VM, è possibile usare Gestione server per rimuovere il ruolo server desktop remoto.

Per altre informazioni, vedere il post di blog relativo all' [errore della VM di Azure "Non sono disponibili server licenze di Desktop remoto"](https://blogs.msdn.microsoft.com/mast/2014/01/21/rdp-to-azure-vm-fails-with-no-remote-desktop-license-servers-available/).

<a id="rdpname"></a>

## <a name="remote-desktop-cant-find-the-computer-name"></a>Desktop remoto: impossibile rilevare il "nome" del computer.
Causa: il client Desktop remoto del computer non è in grado di risolvere il nome del computer nelle impostazioni del file RDP.

Possibili soluzioni:

* Se si usa una rete Intranet aziendale, assicurarsi che il computer abbia accesso al server proxy e sia in grado di inviare a quest'ultimo traffico HTTPS.
* Se si usa un file RDP archiviato localmente, provare a usare il file generato dal portale. Questo passaggio consente di verificare di usare il nome DNS corretto per la macchina virtuale o il servizio cloud e la porta dell'endpoint della VM. Di seguito viene riportato un esempio di file RDP generato dal portale:
  
        full address:s:tailspin-azdatatier.cloudapp.net:55919
        prompt for credentials:i:1

La parte dell'indirizzo del file RDP contiene:

* Il nome di dominio completo del servizio cloud contenente la macchina virtuale ("tailspin-azdatatier.cloudapp.net" in questo esempio).
* La porta TCP esterna dell'endpoint per il traffico di Desktop remoto (55919).

<a id="rdpauth"></a>

## <a name="an-authentication-error-has-occurred-the-local-security-authority-cannot-be-contacted"></a>Si è verificato un errore di autenticazione. Impossibile contattare l'autorità di sicurezza locale.
Causa: la macchina virtuale di destinazione non è in grado di individuare l'autorità di sicurezza nella porzione di nome utente delle credenziali.

Quando il nome utente è nel formato *AutoritàSicurezza*\\*NomeUtente* (esempio: CORP\Utente1), la parte *AutoritàSicurezza* indica o il nome del computer della VM (per l'autorità di protezione locale) o un nome di dominio di Active Directory.

Possibili soluzioni:

* Se l'account è locale nella macchina virtuale, verificare che il nome della macchina virtuale sia stato digitato correttamente.
* Se l'account è in un dominio di Active Directory, controllare l'ortografia del nome di dominio.
* Se è un account di dominio di Active Directory e il nome di dominio è stato digitato correttamente, verificare che in tale dominio sia disponibile un controller di dominio. Nelle reti virtuali di Azure che contengono controller di dominio, è un problema comune che un controller di dominio non sia disponibile perché non è stato avviato. Per risolvere il problema, è possibile usare un account amministratore locale anziché un account di dominio.

<a id="wincred"></a>

## <a name="windows-security-error-your-credentials-did-not-work"></a>Errore di sicurezza di Windows: Le credenziali specificate non funzionano.
Causa: la macchina virtuale di destinazione non può convalidare il nome e la password dell'account.

Un computer basato su Windows può convalidare le credenziali di un account locale o di un account di dominio.

* Per gli account locali, usare la sintassi *NomeComputer*\\*NomeUtente* (ad esempio: SQL1\Admin4798).
* Per gli account di dominio usare la sintassi *NomeDominio*\\*NomeUtente* (ad esempio: CONTOSO\peterodman).

Se la VM è stata innalzata al livello di controller di dominio in una nuova foresta Active Directory, l'account amministratore locale con il quale è stato eseguito l'accesso viene convertito in un account equivalente con la stessa password nella nuova foresta e nel nuovo dominio. L'account locale viene quindi eliminato.

Ad esempio, se è stato eseguito l'accesso con l'account locale DC1\DCAdmin e la macchina virtuale è stata innalzata al livello di controller di dominio in una nuova foresta per il dominio corp.contoso.com, l'account locale DC1\DCAdmin viene eliminato e viene creato un nuovo account di dominio (CORP\DCAdmin) con la stessa password.

Assicurarsi che il nome dell'account sia un nome che possa essere verificato come account valido dalla macchina virtuale e che la password sia corretta.

Se è necessario modificare la password dell'account amministratore locale, vedere [Come reimpostare una password o il servizio Desktop remoto per le macchine virtuali Windows](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

<a id="rdpconnect"></a>

## <a name="this-computer-cant-connect-to-the-remote-computer"></a>Il computer non è in grado di connettersi al computer remoto.
Causa: l'account usato per la connessione non dispone dei diritti di accesso Desktop remoto.

Ogni computer Windows dispone di un gruppo locale Utenti desktop remoto, che contiene gli account e i gruppi che hanno il diritto di accedere in remoto. Anche i membri del gruppo Administrators locale dispongono dell'accesso, sebbene tali account non siano elencati nel gruppo locale Utenti Desktop remoto. Per le macchine appartenenti a un dominio, il gruppo Administrators locale contiene anche gli amministratori di dominio per il dominio.

Assicurarsi che l'account che si usa per la connessione disponga dei diritti di accesso a Desktop remoto. Come soluzione alternativa, usare un account amministratore locale o di dominio per connettersi tramite Desktop remoto. Per aggiungere l'account desiderato al gruppo locale Utenti desktop remoto, usare quindi lo snap-in Microsoft Management Console (**Utilità di sistema > Utenti e gruppi locali > Gruppi > Utenti desktop remoto**).

## <a name="next-steps"></a>Passaggi successivi
Se non è stato visualizzato alcuno di questi messaggi ma si verifica un errore sconosciuto riguardo alla connessione mediante RDP, vedere la [guida alla risoluzione dei problemi relativi a Desktop remoto](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

* Per la risoluzione dei problemi di accesso alle applicazioni in esecuzione in una VM, vedere [Troubleshoot access to an application running on an Azure VM](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Risolvere i problemi di accesso a un'applicazione in esecuzione in una macchina virtuale di Azure).
* Se si verificano problemi relativi all'uso di SSH (Secure Shell) per la connessione a una VM Linux in Azure, vedere [Risoluzione dei problemi di connessione SSH a una macchina virtuale Linux di Azure](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

