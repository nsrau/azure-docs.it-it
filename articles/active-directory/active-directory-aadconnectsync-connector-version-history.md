<properties
   pageTitle="Cronologia del rilascio delle versioni dei connettori | Microsoft Azure"
   description="Questo argomento include l'elenco di tutte le versioni dei connettori per Forefront Identity Manager (FIM) e Microsoft Identity Manager (MIM)"
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>  

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/17/2016"
   ms.author="andkjell"/>  

# Cronologia di rilascio delle versioni dei connettori
I connettori per Forefront Identity Manager (FIM) e Microsoft Identity Manager (MIM) vengono aggiornati frequentemente.

>[AZURE.NOTE]
L'argomento riguarda solo FIM e MIM. Questi connettori non sono supportati in Azure AD Connect.

L'argomento include l'elenco di tutte le versioni dei connettori rilasciate.

Collegamenti correlati:

- [Scaricare i connettori più recenti](http://go.microsoft.com/fwlink/?LinkId=717495)
- Documentazione di riferimento sul [connettore LDAP generico](active-directory-aadconnectsync-connector-genericldap.md)
- Documentazione di riferimento sul [connettore SQL generico](active-directory-aadconnectsync-connector-genericsql.md)
- Documentazione di riferimento sul [connettore per Servizi Web](http://go.microsoft.com/fwlink/?LinkID=226245)
- Documentazione di riferimento sul [connettore PowerShell](active-directory-aadconnectsync-connector-powershell.md)
- Documentazione di riferimento sul [connettore Lotus Domino](active-directory-aadconnectsync-connector-domino.md)

## 1\.1.117.0
Data di rilascio: marzo 2016

**Nuovo connettore** Versione iniziale del [connettore SQL generico](active-directory-aadconnectsync-connector-genericsql.md).

**Nuove funzionalità:**

- Connettore Generic LDAP:
    - Aggiunta del supporto per l'importazione differenziale con Isode.
- Connettore Web Services:
    - Aggiornamento dell’attività csEntryChangeResult e dell’attività setImportErrorCode per consentire che gli errori a livello di oggetto siano restituiti al motore di sincronizzazione.
    - Aggiornamento dei modelli SAP6 e SAP6User per l’uso della nuova funzionalità di errore a livello di oggetto.
- Connettore Lotus Domino:
    - Per l'esportazione è necessario un file di certificazione per ogni rubrica. È ora possibile usare la stessa password per tutti i file di certificazione semplificando la gestione.

**Problemi risolti:**

- Connettore Generic LDAP:
    - Per IBM Tivoli DS alcuni attributi di riferimento non venivano rilevati correttamente.
    - Per Open LDAP, nelle importazioni differenziali, gli spazi vuoti all’inizio e alla fine delle stringhe venivano troncati.
    - Per Novell e NetIQ un'esportazione che sposta un oggetto fra unità organizzative/contenitori e contemporaneamente rinominano l'oggetto non riescono.
- Connettore Web Services:
    - Se il servizio Web aveva più endpoint per lo stesso binding, il connettore non individuava correttamente questi endpoint.
- Connettore Lotus Domino:
    - L’esportazione dell’attributo fullName in un database di posta elettronica in ingresso non funzionava.
    - Le esportazioni che aggiungevano e rimuovevano membri da un gruppo esportavano solo i membri aggiunti.
    - Se un documento di Notes non è valido (l'attributo isValid è impostato su false), l'operazione del connettore non riesce.

## Versioni precedenti
Prima di marzo 2016, i connettori venivano rilasciati come argomenti relativi al supporto.

**Generic LDAP**

- [KB3078617](https://support.microsoft.com/kb/3078617) - 1.0.0597, settembre 2015
- [KB3044896](https://support.microsoft.com/kb/3044896) - 1.0.0549, marzo 2015
- [KB3031009](https://support.microsoft.com/kb/3031009) - 1.0.0534, gennaio 2015
- [KB3008177](https://support.microsoft.com/kb/3008177) - 1.0.0419, settembre 2014
- [KB2936070](https://support.microsoft.com/kb/2936070) - 4.3.1082, marzo 2014

**WebServices**

- [KB3008178](https://support.microsoft.com/kb/3008178) - 1.0.0419, settembre 2014

**PowerShell**

- [KB3008179](https://support.microsoft.com/kb/3008179) - 1.0.0419, settembre 2014

**Lotus Domino**

- [KB3096533](https://support.microsoft.com/kb/3096533) - 1.0.0597, settembre 2015
- [KB3044895](https://support.microsoft.com/kb/3044895) - 1.0.0549, marzo 2015
- [KB2977286](https://support.microsoft.com/kb/2977286) - 5.3.0712, agosto 2014
- [KB2932635](https://support.microsoft.com/kb/2932635) - 5.3.1003, febbraio 2014
- [KB2899874](https://support.microsoft.com/kb/2899874) - 5.3.0721, ottobre 2013
- [KB2875551](https://support.microsoft.com/kb/2875551) - 5.3.0534, agosto 2013

## Passaggi successivi
Ulteriori informazioni sulla configurazione della [sincronizzazione di Azure AD Connect](active-directory-aadconnectsync-whatis.md).

Ulteriori informazioni su [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0928_2016-->