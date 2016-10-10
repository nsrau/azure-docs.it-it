<properties
	pageTitle="Servizio di sincronizzazione Azure AD Connect: Informazioni sulle espressioni di provisioning dichiarativo | Microsoft Azure"
	description="Informazioni sulle espressioni di provisioning dichiarativo."
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="femila"
	editor=""/>  

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/31/2016"
	ms.author="markusvi;andkjell"/>  


# Servizio di sincronizzazione Azure AD Connect: Informazioni sulle espressioni di provisioning dichiarativo
Il servizio di sincronizzazione Azure AD Connect si basa sul provisioning dichiarativo introdotto inizialmente in Forefront Identity Manager 2010. Consente di implementare la logica di business completa per l'integrazione delle identità senza dover scrivere codice compilato.

Una parte essenziale del provisioning dichiarativo è il linguaggio delle espressioni usato nei flussi di attributi. Il linguaggio usato è un subset di Microsoft® Visual Basic®, Applications Edition (VBA). Questo linguaggio viene usato in Microsoft Office e verrà riconosciuto anche dagli utenti con esperienza in VBScript. Il linguaggio delle espressioni di provisioning dichiarativo usa solo funzioni e non è un linguaggio strutturato, né include metodi o istruzioni. Le funzioni sono invece annidate per esprimere il flusso dei programmi.

Per altre informazioni dettagliate, vedere i [riferimenti per il linguaggio Visual Basic, Applications Edition per Office 2013](https://msdn.microsoft.com/library/gg264383.aspx).

Gli attributi sono fortemente tipizzati. Una funzione accetta solo gli attributi del tipo corretto. Fa anche distinzione tra maiuscole e minuscole. Se per i nomi delle funzioni e degli attributi non viene rispettata correttamente la distinzione maiuscole/minuscole, viene generato un errore.

## Definizioni e identificatori del linguaggio

- I nomi delle funzioni sono seguiti dagli argomenti racchiusi tra parentesi: NomeFunzione(argomento 1,argomento N).
- Gli attributi sono identificati da parentesi quadre: [attributeName].
- I parametri sono identificati dal segno di percentuale: %ParameterName%
- Le costanti di stringa sono racchiuse tra virgolette: ad esempio, "Contoso" (Nota: è necessario usare le virgolette semplici "", non quelle non inglesi “”)
- I valori numerici sono espressi senza virgolette e devono essere decimali. Ai valori esadecimali viene aggiunto un prefisso di tipo &H. Ad esempio, 98052, &HFF
- I valori booleani sono espressi con costanti: True, False.
- Le costanti predefinite sono espresse solo tramite il relativo nome: NULL, CRLF, IgnoreThisFlow

### Funzioni
Il provisioning dichiarativo usa molte funzioni per rendere possibile la trasformazione dei valori dell'attributo. Queste funzioni possono essere annidate, in modo che il risultato di una funzione venga passato a un'altra.

`Function1(Function2(Function3()))`  

L'elenco completo delle funzioni è disponibile nel [riferimento di funzione](active-directory-aadconnectsync-functions-reference.md).

### Parametri
Un parametro è definito da un connettore o da un amministratore tramite PowerShell. I parametri includono in genere valori diversi da un sistema all'altro, ad esempio il nome del dominio in cui si trova l'utente. Questi parametri possono essere usati nei flussi degli attributi.

Active Directory Connector fornisce i parametri seguenti per le regole di sincronizzazione in entrata:

| Nome parametro | Commento |
| --- | --- |
| Domain.Netbios | Formato Netbios del dominio attualmente importato, ad esempio FABRIKAMSALES |
| Domain.FQDN | Formato FQDN del dominio attualmente importato, ad esempio sales.fabrikam.com |
| Domain.LDAP | Formato LDAP del dominio attualmente importato, ad esempio DC=sales,DC=fabrikam,DC=com |
| Forest.Netbios | Formato Netbios del nome della foresta attualmente importata, ad esempio FABRIKAMCORP |
| Forest.FQDN | Formato FQDN del nome della foresta attualmente importato, ad esempio FABRIKAMCORP |
| Forest.LDAP | Formato FQDN del nome della foresta attualmente importato, ad esempio DC=fabrikam,DC=com |

Il sistema fornisce il parametro seguente, usato per ottenere l'identificatore del connettore attualmente in esecuzione: `Connector.ID`

L'esempio seguente popola il dominio dell'attributo metaverse con il nome netbios del dominio in cui si trova l'utente: `domain` <- `%Domain.Netbios%`

### Operatori
È possibile usare gli operatori seguenti:

- **Confronto**: <, <=, <>, =, >, >=
- **Matematici**: +, -, *, -
- **Stringa**: & (concatenazione)
- **Logici**: && (AND), || (OR)
- **Ordine di valutazione**: ( )

Gli operatori vengono valutati da sinistra a destra e hanno la stessa priorità di valutazione. Ad esempio, * (moltiplicazione) non viene valutato prima di - (sottrazione). L'operazione 2*(5+3) è diversa da 2*5+3. Le parentesi ( ) vengono usate per modificare l'ordine di valutazione quando un ordine da sinistra a destra non è appropriato.

## Attributi multivalore
Le funzioni possono essere usate con gli attributi a valore singolo e multivalore. Per gli attributi multivalore la funzione agisce su ogni valore e viene applicata la stessa funzione a ogni valore.

Ad esempio `Trim([proxyAddresses])` esegue un trimming di ogni valore nell'attributo proxyAddress. `Word([proxyAddresses],1,"@") & "@contoso.com"` Per ogni valore con una chiocciola (@), sostituire il dominio con @contoso.com. `IIF(InStr([proxyAddresses],"SIP:")=1,NULL,[proxyAddresses])` Cercare l'indirizzo SIP e rimuoverlo dai valori.

## Passaggi successivi

- Per altre informazioni sul modello di configurazione, vedere [Servizio di sincronizzazione Azure AD Connect: Informazioni sul provisioning dichiarativo](active-directory-aadconnectsync-understanding-declarative-provisioning.md).
- Per informazioni sull'uso del provisioning dichiarativo predefinito, vedere [Servizio di sincronizzazione Azure AD Connect: Informazioni sulla configurazione predefinita](active-directory-aadconnectsync-understanding-default-configuration.md).
- Per informazioni su come apportare una modifica pratica con il provisioning dichiarativo, vedere [Servizio di sincronizzazione Azure AD Connect: come apportare modifiche alla configurazione predefinita](active-directory-aadconnectsync-change-the-configuration.md).

**Argomenti generali**

- [Servizio di sincronizzazione Azure AD Connect: Comprendere e personalizzare la sincronizzazione](active-directory-aadconnectsync-whatis.md)
- [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)

**Argomenti di riferimento**

- [Servizio di sincronizzazione Azure AD Connect: Riferimento alle funzioni](active-directory-aadconnectsync-functions-reference.md)

<!---HONumber=AcomDC_0928_2016-->