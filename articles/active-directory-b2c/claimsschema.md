---
title: ClaimsSchema - Azure Active Directory B2C | Microsoft Docs
description: Specificare l'elemento ClaimsSchema di criteri personalizzati in Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 81df3532ee466f32ee42f0010de9aa74dbeb6d85
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64721756"
---
# <a name="claimsschema"></a>ClaimsSchema

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

L'elemento **ClaimsSchema** definisce i tipi di attestazione a cui è possibile fare riferimento nei criteri. Lo schema di attestazioni è il posto in cui si dichiarano le attestazioni. Un'attestazione può essere un nome, un cognome, un nome visualizzato, un numero di telefono e altro ancora. L'elemento ClaimsSchema contiene l'elenco degli elementi **ClaimType**. Un elemento **ClaimType** contiene l'attributo **Id**, che è il nome dell'attestazione. 

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="Id">
      <DisplayName>Surname</DisplayName>
      <DataType>string</DataType>
      <DefaultPartnerClaimTypes>
        <Protocol Name="OAuth2" PartnerClaimType="family_name" />
        <Protocol Name="OpenIdConnect" PartnerClaimType="family_name" />
        <Protocol Name="SAML2" PartnerClaimType="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" />
      </DefaultPartnerClaimTypes>
      <UserHelpText>Your surname (also known as family name or last name).</UserHelpText>
      <UserInputType>TextBox</UserInputType>
```

## <a name="claimtype"></a>ClaimType

L'elemento **ClaimType** contiene l'attributo seguente:

| Attributo | Obbligatoria | DESCRIZIONE |
| --------- | -------- | ----------- |
| ID | Sì | Identificatore usato per il tipo di attestazione. Altri elementi possono usare questo identificatore nei criteri. |

L'elemento **ClaimType** contiene gli elementi seguenti:

| Elemento | Occorrenze | DESCRIZIONE |
| ------- | ----------- | ----------- |
| DisplayName | 0:1 | Titolo che viene visualizzato agli utenti nelle varie schermate. Il valore può essere [localizzato](localization.md). |
| DataType | 0:1 | Tipo di attestazione. È possibile usare i tipi di dati boolean, date, dateTime, int, long, string, stringCollection, alternativeSecurityIdCollection. |
| DefaultPartnerClaimTypes | 0:1 | Tipi di attestazione predefiniti di partner da usare per un protocollo specificato. Il valore può essere sovrascritto nell'elemento **PartnerClaimType** specificato nell'elemento **InputClaim** o **OutputClaim**. Usare questo elemento per specificare il nome predefinito per un protocollo.  |
| Mask | 0:1 | Stringa di caratteri di mascheramento facoltativa che può essere applicata durante la visualizzazione dell'attestazione. Il numero di telefono 324-232-4343 ad esempio può essere mascherato come XXX-XXX-4343. |
| UserHelpText | 0:1 | Descrizione del tipo di attestazione che può essere utile agli utenti per comprenderne lo scopo. Il valore può essere [localizzato](localization.md). |
| UserInputType | 0:1 | Tipo di controllo di input che deve essere disponibile all'utente per l'immissione manuale dei dati di attestazione per il tipo di attestazione. Vedere i tipi di input utente definiti più avanti in questa pagina. |
| Restrizione | 0:1 | Restrizioni ai valori per questa attestazione, ad esempio un'espressione regolare (Regex) o un elenco di valori accettabili. Il valore può essere [localizzato](localization.md). |
PredicateValidationReference| 0:1 | Riferimento a un elemento **PredicateValidationsInput**. Gli elementi **PredicateValidationReference** consentono di eseguire un processo di convalida per garantire l'inserimento solo di dati formattati adeguatamente. Per altre informazioni, vedere [Predicates](predicates.md). |

### <a name="defaultpartnerclaimtypes"></a>DefaultPartnerClaimTypes

L'elemento **DefaultPartnerClaimTypes** può contenere l'elemento seguente:

| Elemento | Occorrenze | DESCRIZIONE |
| ------- | ----------- | ----------- |
| Protocol | 0:n | Elenco di protocolli con il relativo nome del tipo di attestazione di partner predefinito. |

L'elemento **Protocollo** contiene gli attributi seguenti:

| Attributo | Obbligatoria | DESCRIZIONE |
| --------- | -------- | ----------- |
| Name | Sì | Nome di un protocollo valido supportato da Azure AD B2C. I valori possibili sono:  OAuth1, OAuth2, SAML2, OpenIdConnect, WsFed o WsTrust. |
| PartnerClaimType | Sì | Nome del tipo di attestazione da usare. |

Nell'esempio seguente, quando il framework dell'esperienza di gestione delle identità interagisce con un provider di identità SAML2 o un'applicazione basata su attestazioni, l'attestazione **surname** (cognome) viene mappata a `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`. Quando il framework interagisce con OpenIdConnect e OAuth2, l'attestazione viene mappata a `family_name`.

```XML
<ClaimType Id="surname">
  <DisplayName>Surname</DisplayName>
  <DataType>string</DataType>
  <DefaultPartnerClaimTypes>
    <Protocol Name="OAuth2" PartnerClaimType="family_name" />
    <Protocol Name="OpenIdConnect" PartnerClaimType="family_name" />
    <Protocol Name="SAML2" PartnerClaimType="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" />
  </DefaultPartnerClaimTypes>
</ClaimType>
```

Il token JWT emesso da Azure AD B2C crea quindi `family_name` invece del nome di ClaimType **surname** (cognome).
 
```JSON
{
  "sub": "6fbbd70d-262b-4b50-804c-257ae1706ef2",
  "auth_time": 1535013501,
  "given_name": "David",
  "family_name": "Williams",
  "name": "David Williams",
}
```

### <a name="mask"></a>Mask

L'elemento **Mask** contiene gli attributi seguenti:

| Attributo | Obbligatoria | DESCRIZIONE |
| --------- | -------- | ----------- |
| `Type` | Sì | Tipo di maschera dell'attestazione. I valori possibili sono: `Simple` o `Regex`. Il valore `Simple` indica che viene applicata una semplice maschera di testo alla porzione iniziale di un'attestazione di tipo stringa. Il valore `Regex` indica che viene applicata un'espressione regolare all'intera attestazione di tipo stringa.  Se viene specificato il valore `Regex`, è necessario definire anche un attributo facoltativo insieme all'espressione regolare da usare. |
| `Regex` | No  | Se **`Type`** è impostata su `Regex`, specificare l'espressione regolare da utilizzare.

L'esempio seguente configura un'attestazione **PhoneNumber** con la maschera `Simple`:

```XML
<ClaimType Id="PhoneNumber">
  <DisplayName>Phone Number</DisplayName>
  <DataType>string</DataType>
  <Mask Type="Simple">XXX-XXX-</Mask>  
  <UserHelpText>Your telephone number.</UserHelpText>
</ClaimType>
```

Il framework dell'esperienza di gestione delle identità esegue il rendering del numero di telefono e al contempo nasconde le prime sei cifre:

![Utilizzo del tipo di attestazione con maschera (mask)](./media/claimsschema/mask.png)

L'esempio seguente configura un'attestazione **AlternateEmail** con la maschera `Regex`:

```XML
<ClaimType Id="AlternateEmail">
  <DisplayName>Please verify the secondary email linked to your account</DisplayName>
  <DataType>string</DataType>
  <Mask Type="Regex" Regex="(?&lt;=.).(?=.*@)">*</Mask>
  <UserInputType>Readonly</UserInputType>
</ClaimType>
```

Il framework dell'esperienza di gestione delle identità esegue il rendering solo della prima lettera dell'indirizzo e-mail e del nome del dominio di posta elettronica:

![Utilizzo del tipo di attestazione con maschera (mask)](./media/claimsschema/mask-regex.png)


### <a name="restriction"></a>Restrizione

L'elemento **Restriction** può contenere l'attributo seguente:

| Attributo | Obbligatoria | DESCRIZIONE |
| --------- | -------- | ----------- |
| MergeBehavior | No  | Metodo usato per unire i valori di enumerazione con un elemento ClaimType nei criteri padre avente lo stesso identificatore. Usare questo attributo quando si sovrascrive un'attestazione specificata nei criteri di base. I valori possibili sono: `Append`, `Prepend` o `ReplaceAll`. Il valore `Append` è una raccolta di dati che è consigliabile accodare alla fine della raccolta specificata nei criteri padre. Il valore `Prepend` è una raccolta di dati che è consigliabile aggiungere all'inizio della raccolta specificata nei criteri padre. Il `ReplaceAll` valore è una raccolta di dati specificati nei criteri padre che è consigliabile ignorare. |

L'elemento **Restriction** contiene gli elementi seguenti:

| Elemento | Occorrenze | DESCRIZIONE |
| ------- | ----------- | ----------- |
| Enumerazione | 1:n | Opzioni disponibili nell'interfaccia utente che l'utente può selezionare per un'attestazione, ad esempio un valore in un menu a discesa. |
| Modello | 1:1 | Espressione regolare da usare. |

### <a name="enumeration"></a>Enumerazione

L'elemento **Enumeration** contiene gli attributi seguenti:

| Attributo | Obbligatoria | DESCRIZIONE |
| --------- | -------- | ----------- |
| Text | Sì | Stringa di visualizzazione che viene mostrata all'utente nell'interfaccia utente per questa opzione. |
|Value | Sì | Valore di attestazione che viene associato alla selezione di questa opzione. |
| SelectByDefault | No  | Indica se questa opzione deve essere selezionata o meno per impostazione predefinita nell'interfaccia utente. Valori possibili: True o False. |

L'esempio seguente configura un'attestazione di elenco a discesa di **città** con il valore predefinito impostato su `New York`:

```XML
<ClaimType Id="city">
  <DisplayName>city where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="New York" Value="new-york" SelectByDefault="true" />
  </Restriction>
</ClaimType>
```
Elenco a discesa delle città con valore predefinito impostato su New York:

![Elenco a discesa delle città](./media/claimsschema/dropdownsingleselect.png)


### <a name="pattern"></a>Modello

L'elemento **Pattern** può contenere gli attributi seguenti:

| Attributo | Obbligatoria | DESCRIZIONE |
| --------- | -------- | ----------- |
| RegularExpression | Sì | Espressione regolare a cui le attestazioni di questo tipo devono corrispondere per poter essere valide. |
| HelpText | No  | Criterio o espressione regolare per questa attestazione. |

L'esempio seguente configura un'attestazione **messaggio e-mail** con la convalida di input dell'espressione regolare e con il testo della Guida:

```XML
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <DefaultPartnerClaimTypes>
    <Protocol Name="OpenIdConnect" PartnerClaimType="email" />
  </DefaultPartnerClaimTypes>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
  <Restriction>
    <Pattern RegularExpression="^[a-zA-Z0-9.!#$%&amp;'^_`{}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$" HelpText="Please enter a valid email address." />
    </Restriction>
 </ClaimType>
```

Il framework dell'esperienza di gestione delle identità esegue il rendering dell'attestazione indirizzo e-mail con la convalida di input del formato dell'indirizzo di posta elettronica:

![Utilizzo del tipo di attestazione con modello (pattern)](./media/claimsschema/pattern.png)

## <a name="userinputtype"></a>UserInputType

Azure AD B2C supporta molti tipi di input utente, ad esempio casella di testo, password ed elenco a discesa, che possono essere usati durante l'immissione dei dati di attestazione per il tipo di attestazione. È necessario specificare l'elemento **UserInputType** quando si raccolgono le informazioni per l'utente usando un [profilo tecnico autocertificato](self-asserted-technical-profile.md).

### <a name="textbox"></a>TextBox

Il tipo di input utente **TextBox** viene usato per visualizzare una casella di testo a riga singola.

![Utilizzo del tipo di attestazione con casella di testo (textbox)](./media/claimsschema/textbox.png)

```XML
<ClaimType Id="displayName">
  <DisplayName>Display Name</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your display name.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

### <a name="emailbox"></a>EmailBox

Il tipo di input utente **EmailBox** viene usato per visualizzare un campo di input di base per l'e-mail.

![Utilizzo del tipo di attestazione con casella e-mail (emailbox)](./media/claimsschema/emailbox.png)

```XML
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>EmailBox</UserInputType>
  <Restriction>
    <Pattern RegularExpression="^[a-zA-Z0-9!#$%&amp;'+^_`{}~-]+(?:\.[a-zA-Z0-9!#$%&amp;'+^_`{}~-]+)*@(?:[a-zA-Z0-9](?:[a-zA-Z0-9-]*[a-zA-Z0-9])?\.)+[a-zA-Z0-9](?:[a-zA-Z0-9-]*[a-zA-Z0-9])?$" HelpText="Please enter a valid email address." />
  </Restriction>
</ClaimType>
```

### <a name="password"></a>Password

Il tipo di input utente **Password** viene usato per registrare una password immessa dall'utente.

![Utilizzo del tipo di attestazione con password](./media/claimsschema/password.png)

```XML
<ClaimType Id="password">
  <DisplayName>Password</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Enter password</UserHelpText>
  <UserInputType>Password</UserInputType>
</ClaimType>
```

### <a name="datetimedropdown"></a>DateTimeDropdown

Il tipo di input utente **DateTimeDropdown** viene usato per visualizzare un set di menu a discesa da cui selezionare un giorno, un mese e un anno. È possibile usare gli elementi Predicates e PredicateValidations per controllare i valori di data minimo e massimo. Per altre informazioni, vedere la sezione **Configurare un intervallo di date** in [Predicates e PredicateValidations](predicates.md).

![Utilizzo del tipo di attestazione con menu a discesa data e ora (datetimedropdown)](./media/claimsschema/datetimedropdown.png)

```XML
<ClaimType Id="dateOfBirth">
  <DisplayName>Date Of Birth</DisplayName>
  <DataType>date</DataType>
  <UserHelpText>The date on which you were born.</UserHelpText>
  <UserInputType>DateTimeDropdown</UserInputType>
</ClaimType>
```

### <a name="radiosingleselect"></a>RadioSingleSelect

Il tipo di input utente **RadioSingleSelect** viene usato per visualizzare una raccolta di pulsanti radio da cui l'utente può selezionare un'opzione.

![Utilizzo del tipo di attestazione con pulsanti radio a selezione singola (radiodsingleselect)](./media/claimsschema/radiosingleselect.png)

```XML
<ClaimType Id="color">
  <DisplayName>Preferred color</DisplayName>
  <DataType>string</DataType>
  <UserInputType>RadioSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Blue" Value="Blue" SelectByDefault="false" />
    <Enumeration Text="Green " Value="Green" SelectByDefault="false" />
    <Enumeration Text="Orange" Value="Orange" SelectByDefault="true" />
  </Restriction>
</ClaimType>    
```

### <a name="dropdownsingleselect"></a>DropdownSingleSelect

Il tipo di input utente **DropdownSingleSelect** viene usato per visualizzare una casella a discesa da cui l'utente può selezionare un'opzione.

![Utilizzo del tipo di attestazione con casella a discesa a selezione singola (dropdownsingleselect)](./media/claimsschema/dropdownsingleselect.png)

```XML
<ClaimType Id="city">
  <DisplayName>City where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="New York" Value="new-york" SelectByDefault="true" />
  </Restriction>
</ClaimType>
```

### <a name="checkboxmultiselect"></a>CheckboxMultiSelect

Il tipo di input utente **CheckboxMultiSelect** viene usato per visualizzare una raccolta di caselle di controllo da cui l'utente può selezionare più opzioni.

![Utilizzo del tipo di attestazione con casella di controllo a più selezioni (checkboxmultiselect)](./media/claimsschema/checkboxmultiselect.png)

```XML
<ClaimType Id="languages">
  <DisplayName>Languages you speak</DisplayName>
  <DataType>string</DataType>
  <UserInputType>CheckboxMultiSelect</UserInputType>
  <Restriction>
    <Enumeration Text="English" Value="English" SelectByDefault="true" />
    <Enumeration Text="France " Value="France" SelectByDefault="false" />
    <Enumeration Text="Spanish" Value="Spanish" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

### <a name="readonly"></a>Readonly

Il tipo di input utente **Readonly** viene usato per visualizzare un campo di sola lettura in cui visualizzare l'attestazione e il valore.

![Utilizzo del tipo di attestazione con sola lettura (readonly)](./media/claimsschema/readonly.png)

```XML
<ClaimType Id="membershipNumber">
  <DisplayName>Membership number</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your membership number (read only)</UserHelpText>
  <UserInputType>Readonly</UserInputType>
</ClaimType>
```


### <a name="paragraph"></a>Paragraph

Il tipo di input utente **Paragraph** viene usato per visualizzare un campo che mostra solo testo in un tag di paragrafo, ad esempio &lt;p&gt;testo&lt;/p&gt;.

![Utilizzo del tipo di attestazione con paragrafo (paragraph)](./media/claimsschema/paragraph.png)

```XML
<ClaimType Id="responseMsg">
  <DisplayName>Error message: </DisplayName>
  <DataType>string</DataType>
  <AdminHelpText>A claim responsible for holding response messages to send to the relying party</AdminHelpText>
  <UserHelpText>A claim responsible for holding response messages to send to the relying party</UserHelpText>
  <UserInputType>Paragraph</UserInputType>
  <Restriction>
    <Enumeration Text="B2C_V1_90001" Value="You cant sign in because you are a minor" />
    <Enumeration Text="B2C_V1_90002" Value="This action can only be performed by gold members" />
    <Enumeration Text="B2C_V1_90003" Value="You have not been enabled for this operation" />
  </Restriction>
</ClaimType>
```

Per visualizzare uno dei valori di **Enumeration** in un'attestazione **responseMsg** (Msgrisposta), usare la trasformazione di attestazioni `GetMappedValueFromLocalizedCollection` o `CreateStringClaim`. Per altre informazioni, vedere [String Claims Transformations](string-transformations.md) (Trasformazioni di attestazioni stringa) 
