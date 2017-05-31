| **Risorsa** | **Limite predefinito** | **Limite massimo** |
| --- | --- | --- |
| Account Batch per area per sottoscrizione | 3 |50 |
| Core dedicati per account Batch (modalità Servizio Batch)<sup>1</sup> | 20 | N/A<sup>2</sup> |
| Core a bassa priorità per account Batch (modalità Servizio Batch)<sup>3</sup> | 50 | N/A<sup>4</sup> |
| Processi attivi e pianificazioni dei processi<sup>5</sup> per ogni account Batch | 20 | 5000<sup>6</sup> |
| Pool per account Batch | 20 | 2500 |

<sup>1</sup> Le quote di core dedicati riportate riguardano solo gli account in cui la modalità di allocazione pool è impostata su **Servizio Batch**. Per gli account in cui la modalità è impostata su **Sottoscrizione utente**, le quote di core sono basate sulla quota di core delle VM a livello di area o per famiglia di VM nella sottoscrizione.

<sup>2</sup> Il numero di core dedicati per ogni account Batch può essere aumentato, ma il numero massimo non è specificato. Contattare il supporto di Azure per ottenere informazioni sulle opzioni di aumento.

<sup>3</sup> Le quote di core a bassa priorità riportate riguardano solo gli account in cui la modalità di allocazione pool è impostata su **Servizio Batch**. I core a bassa priorità non sono disponibili per gli account in cui la modalità di allocazione pool è impostata su **Sottoscrizione utente**.

<sup>4</sup> Il numero di core a bassa priorità per ogni account Batch può essere aumentato, ma il numero massimo non è specificato. Contattare il supporto di Azure per ottenere informazioni sulle opzioni di aumento.

<sup>5</sup> I processi completati e le pianificazioni dei processi non sono limitati.

<sup>6</sup> Se si vuole richiedere un aumento oltre questo limite, contattare il supporto di Azure.
