---
title: Hybrid Connections overview | Microsoft Docs
description: Learn about Hybrid Connections, security, TCP ports, and supported configurations. MABS, WABS.
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: erikre
editor: ''

ms.assetid: 216e4927-6863-46e7-aa7c-77fec575c8a6
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/18/2016
ms.author: ccompy

---
# Hybrid Connections overview

> [!IMPORTANT]
> BizTalk Hybrid Connections is retired, and replaced by App Service Hybrid Connections. For more information, including how to manage your existing BizTalk Hybrid Connections, see [Azure App Service Hybrid Connections](../app-service/app-service-hybrid-connections.md).

Introduction to Hybrid Connections, lists the supported configurations, and lists the required TCP ports.

## What is a hybrid connection
Hybrid Connections are a feature of Azure BizTalk Services. Hybrid Connections provide an easy and convenient way to connect the Web Apps feature in Azure App Service (formerly Websites) and the Mobile Apps feature in Azure App Service (formerly Mobile Services) to on-premises resources behind your firewall.

![Hybrid Connections][HCImage]

Hybrid Connections benefits include:

* Web Apps and Mobile Apps can access existing on-premises data and services securely.
* Multiple Web Apps or Mobile Apps can share a Hybrid Connection to access an on-premises resource.
* Minimal TCP ports are required to access your network.
* Applications using Hybrid Connections access only the specific on-premises resource that is published through the Hybrid Connection.
* Can connect to any on-premises resource that uses a static TCP port, such as SQL Server, MySQL, HTTP Web APIs, and most custom Web Services.
  
  > [!NOTE]
  > TCP-based services that use dynamic ports (such as FTP Passive Mode or Extended Passive Mode) are currently not supported. LDAP is also not supported. LDAP uses a static TCP port but it could also use UDP. As a result, it is not supported.
  > 
  > 
* Can be used with all frameworks supported by Web Apps (.NET, PHP, Java, Python, Node.js) and Mobile Apps (Node.js, .NET).
* Web Apps and Mobile Apps can access on-premises resources in exactly the same way as if the Web or Mobile App is located on your local network. For example, the same connection string used on-premises can also be used on Azure.

Hybrid Connections also provide enterprise administrators with control and visibility into the corporate resources accessed by hybrid applications, including:

* Using Group Policy settings, administrators can allow Hybrid Connections on the network and also designate resources that can be accessed by hybrid applications.
* Event and audit logs on the corporate network provide visibility into the resources accessed by Hybrid Connections.

## Example scenarios
Hybrid Connections support the following framework and application combinations:

* .NET framework access to SQL Server
* .NET framework access to HTTP/HTTPS services with WebClient
* PHP access to SQL Server, MySQL
* Java access to SQL Server, MySQL and Oracle
* Java access to HTTP/HTTPS services

When using Hybrid Connections to access on-premises SQL Server, consider the following:

* SQL Express Named Instances must be configured to use static ports. By default, SQL Express named instances use dynamic ports.
* SQL Express Default Instances use a static port, but TCP must be enabled. By default, TCP is not enabled.
* When using Clustering or Availability Groups, the `MultiSubnetFailover=true` mode is currently not supported.
* The `ApplicationIntent=ReadOnly` is currently not supported.
* SQL Authentication may be required as the end-to-end authorization method supported by the Azure application and the on-premises SQL server.

## Security and ports
Hybrid Connections use Shared Access Signature (SAS) authorization to secure the connections from the Azure applications and the on-premises Hybrid Connection Manager to the Hybrid Connection. Separate connection keys are created for the application and the on-premises Hybrid Connection Manager. These connection keys can be rolled over and revoked independently.

Hybrid Connections provide for seamless and secure distribution of the keys to the applications and the on-premises Hybrid Connection Manager.

See [Create and Manage Hybrid Connections](integration-hybrid-connection-create-manage.md).

*Application authorization is separate from the Hybrid Connection*. Any appropriate authorization method can be used. The authorization method depends on the end-to-end authorization methods supported across the Azure cloud and the on-premises components. For example, your Azure application accesses an on-premises SQL Server. In this scenario, SQL Authorization may be the authorization method that is supported end-to-end.

#### TCP ports
Hybrid Connections require only outbound TCP or HTTP connectivity from your private network. You do not need to open any firewall ports or change your network perimeter configuration to allow any inbound connectivity into your network.

The following TCP ports are used by Hybrid Connections:

| Port | Why you need it |
| --- | --- |
| 9350 - 9354 |These ports are used for data transmission. The Service Bus relay manager probes port 9350 to determine if TCP connectivity is available. If it is available, then it assumes that port 9352 is also available. Data traffic goes over port 9352. <br/><br/>Allow outbound connections to these ports. |
| 5671 |When port 9352 is used for data traffic, port 5671 is used as the control channel. <br/><br/>Allow outbound connections to this port. |
| 80, 443 |These ports are used for some data requests to Azure. Also, if ports 9352 and 5671 are not usable, *then* ports 80 and 443 are the fallback ports used for data transmission and the control channel.<br/><br/>Allow outbound connections to these ports. <br/><br/>**Note** It is not recommended to use these as the fallback ports in place of the other TCP ports. The HTTP/WebSocket is used as the protocol instead of native TCP for data channels. It could result in lower performance. |

## Next steps
[Create and manage Hybrid Connections](integration-hybrid-connection-create-manage.md)

## See Also
[REST API for Managing BizTalk Services on Microsoft Azure](/previous-versions/azure/reference/dn232347(v=azure.100))  
[BizTalk Services: Editions Chart](biztalk-editions-feature-chart.md)  
[Create a BizTalk Service](biztalk-provision-services.md)  
[BizTalk Services: Dashboard, Monitor and Scale tabs](biztalk-dashboard-monitor-scale-tabs.md)  

[HCImage]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionImage.png
