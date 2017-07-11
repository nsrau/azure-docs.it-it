| Risorsa | Limite predefinito | Limite massimo |
| --- | --- | --- |
| VM per [sottoscrizione](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> per area |10.000 per area |
| Numero totale di core della VM per ogni [sottoscrizione](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> per area | Contattare il supporto tecnico |
| Core di VM per ogni serie (Dv2, F e così via) per ogni [sottoscrizione](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> per area | Contattare il supporto tecnico |
| [Coamministratori](../articles/billing-add-change-azure-subscription-administrator.md) per sottoscrizione |Senza limiti |Senza limiti |
| [Account di archiviazione](../articles/storage/storage-create-storage-account.md) per sottoscrizione |200 |200<sup>2</sup> |
| [Gruppi di risorse](../articles/azure-resource-manager/resource-group-overview.md) per sottoscrizione |800 |800 |
| [Set di disponibilità](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) per sottoscrizione |2.000 per area |2.000 per area |
| Letture API Gestione risorse |15.000 all'ora |15.000 all'ora |
| Scritture API Gestione risorse |1.200 all'ora |1.200 all'ora |
| Dimensioni delle richieste API di gestione delle risorse |4.194.304 byte |4.194.304 byte |
| Tag per sottoscrizione<sup>3</sup> |senza limiti |senza limiti |
| Calcoli di tag univoco per sottoscrizione<sup>3</sup> | 10.000 | 10.000 |
| [Servizi cloud](../articles/cloud-services/cloud-services-choose-me.md) per sottoscrizione |Non applicabile<sup>4</sup> |Non applicabile<sup>4</sup> |
| [Gruppi di affinità](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) per sottoscrizione |Non applicabile<sup>4</sup> |Non applicabile<sup>4</sup> |

<sup>1</sup>I limiti predefiniti variano in base al tipo di categoria di offerta, ad esempio Versione di valutazione gratuita, Pagamento in base al consumo, e al tipo di serie, ad esempio Dv2, F, G e così via.

<sup>2</sup>Sono inclusi gli account di archiviazione Standard e Premium. Se sono necessari più di 200 account di archiviazione, inviare una richiesta tramite il [supporto tecnico di Azure](https://azure.microsoft.com/support/faq/). Il team di Archiviazione di Azure esaminerà il caso aziendale e potrà approvare fino a un massimo di 250 account di archiviazione.

<sup>3</sup>È possibile applicare un numero illimitato di tag per sottoscrizione. Il numero di tag per risorsa o per gruppo di risorse è limitato a 15. Resource Manager restituisce solo un [elenco di nomi e valori di tag univoci](/rest/api/resources/tags#Tags_List) nella sottoscrizione quando il numero di tag non supera i 10.000. È tuttavia possibile trovare comunque una risorsa in base al tag quando il numero è superiore a 10.000.  

<sup>4</sup>Queste funzionalità non sono più necessarie con i gruppi di risorse di Azure e Azure Resource Manager.

> [!NOTE]
> È importante sottolineare che i core delle macchine virtuali devono rispettare un limite totale per l'area, nonché un limite di serie di ridimensionamento (Dv2, F e così via) per l'area, applicati separatamente.  Si consideri ad esempio una sottoscrizione con limite di core di VM totale per gli Stati Uniti orientali pari a 30, un limite di core di serie A pari a 30 e un limite di core di serie D pari a 30.  Questa sottoscrizione sarà autorizzata a distribuire 30 VM A1 o 30 VM D1 o una combinazione delle due che non superi un totale di 30 core, ad esempio 10 VM A1 e 20 VM D1.  
> <!-- -->
> 
> 

