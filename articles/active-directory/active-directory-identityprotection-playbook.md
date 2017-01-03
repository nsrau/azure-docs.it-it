---
title: Studio su Azure Active Directory Identity Protection | Documentazione Microsoft
description: "Informazioni su come Azure AD Identity Protection consente di limitare la possibilità di un utente malintenzionato di sfruttare un&quot;identità o un dispositivo compromesso e di proteggere un&quot;identità o un dispositivo che in precedenza è stato sospettato o ritenuto essere compromesso."
services: active-directory
keywords: "azure active directory identity protection, cloud app discovery, gestione applicazioni, sicurezza, rischio, livello di rischio, vulnerabilità, criteri di sicurezza"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 60836abf-f0e9-459d-b344-8e06b8341d25
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2016
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 77031b3bbe2372bf2cac34bac45368ac40911641
ms.openlocfilehash: 87070d7680a6d48e94099b47f9dc336a75332f6c


---
# <a name="azure-active-directory-identity-protection-playbook"></a>Studio sulla protezione delle identità di Azure Active Directory
Questo studio consente di:

* Inserire i dati nell'ambiente Identity Protection simulando eventi di rischio e vulnerabilità
* Impostare criteri di accesso condizionali basati sui rischi e testare l'impatto di questi criteri

## <a name="simulating-risk-events"></a>Simulazione di eventi di rischio
Questa sezione riporta i passaggi per la simulazione dei seguenti tipi di eventi di rischio:

* Accessi da indirizzi IP anonimi (facile)
* Accessi da posizioni non note (intermedio)
* Trasferimento impossibile a posizioni atipiche (difficile)

Non è possibile simulare altri eventi di rischio in modo sicuro.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Accessi da indirizzi IP anonimi
Questo tipo di evento di rischio identifica gli utenti che hanno eseguito l'accesso da un indirizzo IP riconosciuto come un indirizzo IP proxy anonimo. Questi proxy vengono usati da persone che vogliono nascondere l'indirizzo IP del dispositivo e possono essere usati per attacchi dannosi.

**Per simulare un accesso da un IP anonimo, seguire questa procedura**:

1. Scaricare [Tor Browser](https://www.torproject.org/projects/torbrowser.html.en).
2. Usando Tor Browser, passare ad [https://myapps.microsoft.com](https://myapps.microsoft.com).   
3. Immettere le credenziali dell'account da visualizzare nel report **Accessi da indirizzi IP anonimi** .

L’accesso verrà visualizzato nel dashboard Identity Protection entro 5 minuti. 

### <a name="sign-ins-from-unfamiliar-locations"></a>Accessi da posizioni non note
L'evento di rischio per gli accessi da posizioni non note è un meccanismo di valutazione dell'accesso in tempo reale che prende in considerazione le posizioni di accesso precedenti, come l'indirizzo IP, la latitudine, la longitudine e l'ASN, per determinare posizioni nuove o non note. Il sistema archivia gli indirizzi IP, la latitudine, la longitudine e gli ASN usati in precedenza da un utente e li considera posizioni "note". Una posizione di accesso viene considerata non nota se non corrisponde a nessuna delle posizioni note esistenti.

Azure Active Directory Identity Protection:  

* ha un periodo di apprendimento iniziale di 14 giorni, durante i quali le nuove posizioni non vengono contrassegnate come posizioni non note.
* ignora gli accessi da dispositivi noti e le posizioni geograficamente vicine a una posizione nota esistente.

Per simulare posizioni non note, è necessario accedere da una posizione e da un dispositivo mai usati prima con l'account. 

**Per simulare un accesso da una posizione non nota, seguire questa procedura**:

1. Scegliere un account che abbia una cronologia di accesso di almeno 14 giorni. 
2. È possibile procedere in due modi:
   
   a. Mentre si usa una VPN, passare ad [https://myapps.microsoft.com](https://myapps.microsoft.com) e immettere le credenziali dell'account per cui si vuole simulare l'evento di rischio.
   
   b. Chiedere a un collega in un’altra posizione di accedere usando le credenziali dell'account (scelta non consigliata).

L’accesso verrà visualizzato nel dashboard Identity Protection entro 5 minuti.

### <a name="impossible-travel-to-atypical-location"></a>Trasferimento impossibile a posizioni atipiche
La condizione di trasferimento impossibile è difficile da simulare perché l'algoritmo usa Machine Learning per eliminare i falsi positivi, ad esempio il trasferimento impossibile da dispositivi noti o l'accesso da VPN usate da altri utenti nella directory. Per iniziare a generare eventi di rischio, l'algoritmo richiede anche una cronologia di accesso da 3 a 14 giorni per l'utente.

**Per simulare un trasferimento impossibile a posizioni atipiche, seguire questa procedura**:

1. Usando il browser standard, passare ad [https://myapps.microsoft.com](https://myapps.microsoft.com).  
2. Immettere le credenziali dell'account per cui si vuole generare un evento di rischio trasferimento impossibile.
3. Modificare l'agente utente. Per modificare l'agente utente in Internet Explorer, usare Strumenti di sviluppo. Per modificare l'agente utente in Firefox o Chrome, usare un componente aggiuntivo di selezione dell'agente utente.
4. Modificare l'indirizzo IP. È possibile modificare l'indirizzo IP usando una VPN, un componente aggiuntivo Tor o avviare un nuovo computer in Azure in un altro data center.
5. Accedere ad [https://myapps.microsoft.com](https://myapps.microsoft.com) con le stesse credenziali usate in precedenza ed entro pochi minuti dall'accesso precedente.

L’accesso verrà visualizzato nel dashboard Identity Protection entro 2-4 ore.<br>
A causa dei complessi modelli di Machine Learning coinvolti, potrebbe non essere rilevato.<br>  Potrebbe essere utile ripetere questi passaggi per più account Azure AD.

## <a name="simulating-vulnerabilities"></a>Simulazione di vulnerabilità
Le vulnerabilità sono punti deboli in un ambiente Azure AD che possono essere sfruttati da un utente malintenzionato. In Azure AD Identity Protection sono attualmente visibili 3 tipi di vulnerabilità che consentono di sfruttare altre funzionalità di Azure AD. Tali vulnerabilità verranno visualizzate automaticamente nel dashboard Identity Protection dopo aver impostato le funzionalità seguenti.

* Azure AD [Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)
* Azure AD [Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md).
* Azure AD [Privileged Identity Management](active-directory-privileged-identity-management-configure.md). 

## <a name="user-compromise-risk"></a>Rischio di compromissione dell'utente
**Per testare il rischio di compromissione dell'utente, seguire questa procedura**:

1. Accedere ad [https://portal.azure.com](https://portal.azure.com) con le credenziali di amministratore globale del tenant.
2. Passare a **Identity Protection**. 
3. Nel pannello principale di **Azure AD Identity Protection** fare clic su **Impostazioni**. 
4. Nel pannello **Impostazioni del portale** in **Regole di sicurezza** fare clic su **Rischio di compromissione dell'utente**. 
5. Nel pannello **Rischio di accesso** disattivare **Abilita regola** e quindi fare clic su **Salva** per salvare le impostazioni.
6. Per un determinato account utente, simulare un evento di rischio IP anonimo o posizione non nota. In questo modo il livello di rischio utente verrà innalzato a **Medio**per tale utente.
7. Attendere alcuni minuti e poi verificare che il livello utente sia **Medio**.
8. Passare al pannello **Impostazioni del portale** .
9. Nel pannello **Rischio di compromissione dell'utente** in **Abilita regola** selezionare **Sì**. 
10. Selezionare una delle opzioni seguenti:
    
    a. Per bloccare, selezionare **Medio** in **Blocca l'accesso**.
    
    b. Per applicare la modifica della password di protezione, selezionare **Medio** in **Richiedi autenticazione a più fattori**.
11. Fare clic su **Save**.
12. A questo punto è possibile testare l'accesso condizionale basato sul rischio eseguendo l'accesso come un utente con un livello di rischio elevato. Se il rischio dell'utente è medio, l'accesso verrà bloccato o verrà richiesto di modificare la password, a seconda della configurazione dei criteri. 
    <br><br>
    ![Playbook](./media/active-directory-identityprotection-playbook/201.png "Playbook")
    <br>

## <a name="sign-in-risk"></a>Rischio di accesso
**Per testare un rischio di accesso, seguire questa procedura:**

1. Accedere ad [https://portal.azure.com ](https://portal.azure.com) con le credenziali di amministratore globale del tenant.
2. Passare a **Identity Protection**.
3. Nel pannello principale di **Azure AD Identity Protection** fare clic su **Impostazioni**. 
4. Nel pannello **Impostazioni del portale** in **Regole di sicurezza** fare clic su **Rischio di accesso**.
5. Nel pannello **Rischio di accesso** selezionare **Sì** in **Abilita regola**. 
6. Selezionare una delle opzioni seguenti:
   
   a. Per bloccare, selezionare **Medio** in **Blocca l'accesso**
   
   b. Per applicare la modifica della password di protezione, selezionare **Medio** in **Richiedi autenticazione a più fattori**.
7. Per bloccare, selezionare Medio in Blocca l'accesso.
8. Per applicare l'autenticazione a più fattori, selezionare **Medio** in **Richiedi autenticazione a più fattori**.
9. Fare clic su **Save**.
10. A questo punto è possibile testare l'accesso condizionale basato sul rischio simulando gli eventi di rischio relativi a posizioni insolite o indirizzi IP anonimi, perché sono entrambi eventi di rischio di livello **Medio** .


![Playbook](./media/active-directory-identityprotection-playbook/200.png "Playbook")


## <a name="see-also"></a>Vedere anche
* [Azure Active Directory Identity Protection](active-directory-identityprotection.md)




<!--HONumber=Jan17_HO1-->


