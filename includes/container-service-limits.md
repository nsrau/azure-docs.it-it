| Risorsa | Limite predefinito |
| --- | :--- |
| Numero massimo nodi per cluster | 100 |
| Numero massimo pod per nodo ([rete di base con Kubenet][basic-networking]) | 110 |
| Numero massimo pod per nodo ([rete avanzata con l'interfaccia di rete dei contenitori di Azure][advanced-networking]) | 30<sup>1</sup> |
| Numero massimo cluster per sottoscrizione | 100 |

<sup>1</sup> Questo valore può essere personalizzato tramite la distribuzione di modelli di Azure Resource Manager. Alcuni esempi sono disponibili [qui][arm-deployment-example].<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/networking-overview.md#basic-networking
[advanced-networking]: ../articles/aks/networking-overview.md#advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[arm-deployment-example]: https://github.com/Azure/AKS/blob/master/examples/vnet/02-aks-custom-vnet.json#L64-L69
