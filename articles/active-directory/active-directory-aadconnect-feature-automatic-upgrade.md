<properties
   pageTitle="Azure AD Connect: aggiornamento automatico | Microsoft Azure"
   description="Questo argomento illustra la funzionalità di aggiornamento automatico predefinita nel servizio di sincronizzazione Azure AD Connect."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="02/16/2016"
   ms.author="andkjell"/>

# Azure AD Connect: aggiornamento automatico
Questa funzionalità è stata introdotta nella build 1.1.105.0 rilasciata nel mese di febbraio 2016.

## Panoramica
La funzionalità per l'**aggiornamento automatico** semplifica al massimo la verifica dell'aggiornamento dell'installazione di Azure AD Connect. Questa funzionalità è abilitata per impostazione predefinita per le installazioni rapide e consente di assicurare che l'installazione venga aggiornata automaticamente al rilascio di una nuova versione.

L'aggiornamento automatico è abilitato per impostazione predefinita per gli scenari seguenti:

- Installazione mediante le impostazioni rapide
- Uso di un database locale di SQL Express richiesto sempre dalle impostazioni rapide
- L'account AD è l'account MSOL\_ predefinito creato dalle impostazioni rapide
- Il metaverse include meno di 100.000 oggetti

Lo stato corrente dell'aggiornamento automatico può essere visualizzato con il cmdlet `Get-ADSyncAutoUpgrade` di PowerShell e include gli stati seguenti:

| Stato | Commento |
| ---- | ---- |
| Enabled | L'aggiornamento automatico è abilitato. |
| Suspended | Impostato solo dal sistema. Il sistema non è più idoneo per la ricezione di aggiornamenti automatici. |
| Disabled | L'aggiornamento automatico è disabilitato. |

Per passare da **Enabled** a **Disabled**, è possibile usare `Set-ADSyncAutoUpgrade`. Lo stato **Suspended** deve essere impostato solo dal sistema.

L'aggiornamento automatico usa Azure AD Connect Health come infrastruttura di aggiornamento. Per un corretto funzionamento dell'aggiornamento automatico, assicurarsi di avere aperto gli URL nel proxy per **Azure AD Connect Health**, come documentato in [URL e intervalli di indirizzi IP per Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

Se l'interfaccia utente di **Synchronization Service Manager** è in esecuzione sul server, l'aggiornamento verrà sospeso fino alla chiusura dell'interfaccia utente.

## Passaggi successivi
Ulteriori informazioni su [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0218_2016-->