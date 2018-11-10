---
title: 'Azure AD Connect: Informazioni sul modulo di PowerShell ADConnectivityTool | Microsoft Docs'
description: Questo documento illustra il nuovo modulo di PowerShell ADConnectivity
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 10/19/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 4020d754bd8c7728b43ef583aa7e7d8dbddcebec
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2018
ms.locfileid: "50669798"
---
# <a name="what-is-the-adconnectivitytool-powershell-module"></a>Informazioni sul modulo ADConnectivityTool di PowerShell

Lo strumento ADConnectivity è un modulo di PowerShell che viene usato in una delle situazioni seguenti:

- Durante l'installazione quando un problema di connettività di rete impedisce di convalidare correttamente le credenziali di Active Directory fornite dall'utente nella procedura guidata.
- Dopo l'installazione da parte di un utente che chiama le funzioni da una sessione di PowerShell.

Lo strumento si trova in: **C:\Programmi\Microsoft Azure Active Directory Connect\Tools\ ADConnectivityTool.psm1** 

## <a name="adconnectivitytool-during-installation"></a>ADConnectivityTool durante l'installazione

Nella pagina **Connessione delle directory** della procedura guidata di Azure AD Connect, se si verifica un problema di rete, ADConnectivityTool userà automaticamente una delle funzioni per determinare che cosa sta accadendo.  I seguenti possono essere considerati problemi di rete:

- Il nome della foresta specificato dall'utente non è stato digitato correttamente o è stato visualizzato un messaggio che indica che la foresta non esiste 
- La porta UDP 389 è chiusa nei controller di dominio associati alla foresta specificata dall'utente
- Le credenziali fornite nella finestra "Account della foresta AD" non hanno i privilegi necessari per recuperare i controller di dominio associati alla foresta di destinazione
- Una o più delle porte TCP 53, 88 o 389 sono chiuse nei controller di dominio associati alla foresta specificata dall'utente 
- Sia la porta UDP 389 che una o più porte TCP sono chiuse
- Non è possibile risolvere DNS per la foresta fornita e i controller di dominio associati

Quando si verifica uno o più di questi problemi, nella procedura guidata di AADConnect viene visualizzato un messaggio di errore correlato:


![Tipi di errore](media\how-to-connect-adconnectivitytools\error1.png)

Quando ad esempio si prova ad aggiungere una directory nella schermata **Connessione delle directory**, Azure AD Connect deve verificarla e deve poter comunicare con un controller di dominio tramite la porta 389.  Se non riesce, viene visualizzato l'errore mostrato nello screenshot precedente.  

Ciò che effettivamente accade in background è che Azure AD Connect chiama la funzione `Start-NetworkConnectivityDiagnosisTools`.  Questa funzione viene chiamata quando la convalida delle credenziali non riesce a causa di un problema di connettività di rete.

Viene infine generato un file di log dettagliato ogni volta che lo strumento viene chiamato dalla procedura guidata. Il log si trova in **C:\ProgramData\AADConnect\ADConnectivityTool-<date>-<time>.log**

## <a name="adconnectivitytools-post-installation"></a>ADConnectivityTools dopo l'installazione
Dopo l'installazione di Azure AD Connect, è possibile usare qualsiasi funzione del modulo di PowerShell ADConnectivityTools.  

Per informazioni di riferimento sulle funzioni, vedere [Informazioni di riferimento su ADConnectivityTools](reference-connect-adconnectivitytools.md)

### <a name="start-connectivityvalidation"></a>Start-ConnectivityValidation

Verrà chiamata questa funzione perché può essere chiamata **solo** manualmente dopo l'importazione di ADConnectivityTool.psm1 in PowerShell. 

Questa funzione esegue la stessa logica eseguita dalla procedura guidata di Azure AD Connect per convalidare le credenziali di AD specificate.  Fornisce tuttavia una spiegazione molto più dettagliata del problema e una soluzione suggerita. 

La convalida della connettività prevede i passaggi seguenti:
-   Ottenere l'oggetto nome di dominio completo (FQDN)
-   Verificare che, se l'utente ha selezionato "Crea un nuovo account AD", queste credenziali appartengano al gruppo Amministratori azienda
-   Ottenere l'oggetto FQDN della foresta
-   Verificare che almeno un dominio associato all'oggetto FQDN della foresta ottenuto in precedenza sia raggiungibile
-   Verificare che il livello funzionale della foresta sia Windows Server 2003 o versione successiva.

L'utente potrà aggiungere una directory se tutte queste azioni vengono eseguite correttamente.

Se l'utente esegue questa funzione dopo che un problema è stato risolto (o se non esistono problemi) l'output indicherà all'utente di tornare alla procedura guidata di Azure AD Connect Wizard e di provare a inserire nuovamente le credenziali.



## <a name="next-steps"></a>Passaggi successivi
- [Azure AD Connect: account e autorizzazioni](reference-connect-accounts-permissions.md)
- [Installazione rapida](how-to-connect-install-express.md)
- [Installazione personalizzata](how-to-connect-install-custom.md)
- [Informazioni di riferimento su ADConnectivityTools](reference-connect-adconnectivitytools.md)

