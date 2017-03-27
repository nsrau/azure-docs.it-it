---
title: Cronologia del rilascio delle versioni dei connettori | Documentazione Microsoft
description: Questo argomento include l&quot;elenco di tutte le versioni dei connettori per Forefront Identity Manager (FIM) e Microsoft Identity Manager (MIM)
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: 6a0c66ab-55df-4669-a0c7-1fe1a091a7f9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/08/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 3051ed0385b81892b8495e83817ed8255dbce8cd
ms.lasthandoff: 03/14/2017


---
# <a name="connector-version-release-history"></a>Cronologia di rilascio delle versioni dei connettori
I connettori per Forefront Identity Manager (FIM) e Microsoft Identity Manager (MIM) vengono aggiornati frequentemente.

> [!NOTE]
> L'argomento riguarda solo FIM e MIM. Questi connettori non sono supportati in Azure AD Connect.

L'argomento include l'elenco di tutte le versioni dei connettori rilasciate.

Collegamenti correlati:

* [Scaricare i connettori più recenti](http://go.microsoft.com/fwlink/?LinkId=717495)
* [connettore LDAP generico](active-directory-aadconnectsync-connector-genericldap.md) 
* [connettore SQL generico](active-directory-aadconnectsync-connector-genericsql.md) 
* [connettore per Servizi Web](http://go.microsoft.com/fwlink/?LinkID=226245) 
* [connettore PowerShell](active-directory-aadconnectsync-connector-powershell.md) 
* [connettore Lotus Domino](active-directory-aadconnectsync-connector-domino.md) 

## <a name="114430"></a>1.1.443.0

Data di rilascio: marzo 2017

### <a name="enhancements"></a>Miglioramenti
* Generic SQL:</br>
  **Sintomi dello scenario:** è un limite noto del connettore SQL quando si consente il riferimento a un solo tipo di oggetto e si richiede un riferimento incrociato con i membri. </br>
  **Descrizione della soluzione:** nella fase di elaborazione per i riferimenti in cui si sceglie l'opzione "*", TUTTI i tipi di oggetto saranno restituiti al motore di sincronizzazione.

>[!Important]
- In questo modo vengono creati molti segnaposti
- È necessario assicurarsi che il nome sia univoco tra i tipi di oggetto.


* Generic LDAP:</br>
 **Scenario:** anche quando vengono selezionati solo alcuni contenitori nella partizione specifica, la ricerca viene comunque eseguita nell'intera partizione. La partizione specifica sarà filtrata dal servizio di sincronizzazione ma non dal MA, il che potrebbe causare un calo delle prestazioni. </br>

 **Descrizione della soluzione:** il codice del connettore GLDAP modificato consente di esaminare tutti i contenitori e cercare oggetti in ciascuno di essi, anziché cercare nell'intera partizione.


* Lotus Domino:

  **Scenario:** supporto dell'eliminazione della posta di Domino per la rimozione di un utente durante un'esportazione. </br>
  **Soluzione:** supporto dell'eliminazione della posta configurabile per la rimozione di un utente durante un'esportazione.

### <a name="fixed-issues"></a>Problemi risolti:
* Servizi Web generici:
 * Quando si modifica l'URL del servizio nei progetti predefiniti SAP wsconfig tramite lo strumento di configurazione del servizio Web si verifica l'errore seguente: Impossibile trovare una parte del percorso

      ``'C:\Users\cstpopovaz\AppData\Local\Temp\2\e2c9d9b0-0d8a-4409-b059-dceeb900a2b3\b9bedcc0-88ac-454c-8c69-7d6ea1c41d17\cfg.config\cloneconfig.xml'. ``

* Generic LDAP:
 * Correzione per il bug dell'attributo multivalore non importato per l'importazione differenziale watermark di Generic SQL
 * Il connettore GLDAP non vede tutti gli attributi in AD LDS
 * La procedura guidata si interrompe quando non viene rilevato alcun attributo UPN dallo schema di directory LDAP
 * Le importazioni differenziali generano errori di individuazione non presenti durante l'importazione completa, quando l'attributo "objectclass" non è selezionato
 * La pagina di configurazione "Configurare partizioni e gerarchie" non mostra gli oggetti il cui tipo è uguale alla partizione per i server Novel nel MA Generic  
LDAP. Essi mostravano solo gli oggetti della partizione di RootDSE.


* Generic SQL:
 * Quando si esportano valori di attributi multivalore eliminati/aggiunti, non vengono eliminati/aggiunti nell'origine dati.  


* Lotus Notes:
 * Un campo specifico "Cognome" è visualizzato correttamente nel metaverse, tuttavia durante l'esportazione in Notes il valore dell'attributo è Null o Empty.
 * Correzione per l'errore di file di certificazione duplicato
 * Quando si seleziona l'oggetto senza dati nel connettore Lotus Domino con altri oggetti, viene visualizzato l'errore di individuazione durante un'importazione completa.
 * Quando viene eseguita un'importazione differenziale nel connettore Lotus Domino, alla fine dell'esecuzione il servizio Microsoft.IdentityManagement.MA.LotusDomino.Service.exe talvolta restituisce un errore dell'applicazione.
 * L'appartenenza ai gruppi nel complesso funziona bene e viene mantenuta, tranne quando l'esportazione per tentare di rimuovere un utente dall'appartenenza viene completata correttamente con un aggiornamento ma l'utente non viene effettivamente rimosso dall'appartenenza a Lotus Notes.
 * La possibilità di scegliere la modalità di esportazione "Aggiungi elemento in fondo" è stato aggiunta nell'interfaccia utente grafica di Lotus MA per accodare i nuovi elementi in fondo durante l'esportazione per gli attributi multivalore.
 * Il connettore aggiungerà la logica necessaria per eliminare il file dalla cartella di posta elettronica e l'insieme di credenziali degli ID.
 * Eliminare l'appartenenza non funziona per un membro NAB trasversale.
 * L'eliminazione dei valori da un attributo multivalore dovrebbe riuscire

## <a name="111170"></a>1.1.117.0
Data di rilascio: marzo 2016

**Nuovo connettore**  
Versione iniziale del [connettore SQL generico](active-directory-aadconnectsync-connector-genericsql.md).

**Nuove funzionalità:**

* Connettore Generic LDAP:
  * Aggiunta del supporto per l'importazione differenziale con Isode.
* Connettore Web Services:
  * Aggiornamento dell’attività csEntryChangeResult e dell’attività setImportErrorCode per consentire che gli errori a livello di oggetto siano restituiti al motore di sincronizzazione.
  * Aggiornamento dei modelli SAP6 e SAP6User per l’uso della nuova funzionalità di errore a livello di oggetto.
* Connettore Lotus Domino:
  * Per l'esportazione è necessario un file di certificazione per ogni rubrica. È ora possibile usare la stessa password per tutti i file di certificazione semplificando la gestione.

**Problemi risolti:**

* Connettore Generic LDAP:
  * Per IBM Tivoli DS alcuni attributi di riferimento non venivano rilevati correttamente.
  * Per Open LDAP, nelle importazioni differenziali, gli spazi vuoti all’inizio e alla fine delle stringhe venivano troncati.
  * Per Novell e NetIQ un'esportazione che sposta un oggetto fra unità organizzative/contenitori e contemporaneamente rinominano l'oggetto non riescono.
* Connettore Web Services:
  * Se il servizio Web aveva più endpoint per lo stesso binding, il connettore non individuava correttamente questi endpoint.
* Connettore Lotus Domino:
  * L’esportazione dell’attributo fullName in un database di posta elettronica in ingresso non funzionava.
  * Le esportazioni che aggiungevano e rimuovevano membri da un gruppo esportavano solo i membri aggiunti.
  * Se un documento di Notes non è valido (l'attributo isValid è impostato su false), l'operazione del connettore non riesce.

## <a name="older-releases"></a>Versioni precedenti
Prima di marzo 2016, i connettori venivano rilasciati come argomenti relativi al supporto.

**Generic LDAP**

* [KB3078617](https://support.microsoft.com/kb/3078617) - 1.0.0597, settembre 2015
* [KB3044896](https://support.microsoft.com/kb/3044896) - 1.0.0549, marzo 2015
* [KB3031009](https://support.microsoft.com/kb/3031009) - 1.0.0534, gennaio 2015
* [KB3008177](https://support.microsoft.com/kb/3008177) - 1.0.0419, settembre 2014
* [KB2936070](https://support.microsoft.com/kb/2936070) - 4.3.1082, marzo 2014

**WebServices**

* [KB3008178](https://support.microsoft.com/kb/3008178) - 1.0.0419, settembre 2014

**PowerShell**

* [KB3008179](https://support.microsoft.com/kb/3008179) - 1.0.0419, settembre 2014

**Lotus Domino**

* [KB3096533](https://support.microsoft.com/kb/3096533) - 1.0.0597, settembre 2015
* [KB3044895](https://support.microsoft.com/kb/3044895) - 1.0.0549, marzo 2015
* [KB2977286](https://support.microsoft.com/kb/2977286) - 5.3.0712, agosto 2014
* [KB2932635](https://support.microsoft.com/kb/2932635) - 5.3.1003, febbraio 2014  
* [KB2899874](https://support.microsoft.com/kb/2899874) - 5.3.0721, ottobre 2013
* [KB2875551](https://support.microsoft.com/kb/2875551) - 5.3.0534, agosto 2013

## <a name="next-steps"></a>Passaggi successivi
Ulteriori informazioni sulla configurazione della [sincronizzazione di Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

Ulteriori informazioni su [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).

