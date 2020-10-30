---
title: Następne kroki — DevOps z ASP.NET Core i platformą Azure
author: CamSoper
description: Dodatkowe ścieżki szkoleniowe dla DevOps z ASP.NET Core i platformą Azure.
ms.author: casoper
ms.custom: devx-track-csharp, mvc, seodec18
ms.date: 10/24/2018
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: azure/devops/next-steps
ms.openlocfilehash: 35b0486611cc15f25b1c8b54584c264e4c1298c9
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056598"
---
# <a name="next-steps"></a><span data-ttu-id="f4bc7-103">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="f4bc7-103">Next steps</span></span>

<span data-ttu-id="f4bc7-104">W tym przewodniku utworzono potok DevOps dla przykładowej aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f4bc7-104">In this guide, you created a DevOps pipeline for an ASP.NET Core sample app.</span></span> <span data-ttu-id="f4bc7-105">Gratulacje!</span><span class="sxs-lookup"><span data-stu-id="f4bc7-105">Congratulations!</span></span> <span data-ttu-id="f4bc7-106">Mamy nadzieję, że zachęcasz do publikowania ASP.NET Core aplikacji sieci Web w celu Azure App Service i zautomatyzowania ciągłej integracji zmian.</span><span class="sxs-lookup"><span data-stu-id="f4bc7-106">We hope you enjoyed learning to publish ASP.NET Core web apps to Azure App Service and automate the continuous integration of changes.</span></span>

<span data-ttu-id="f4bc7-107">Poza hostingiem sieci Web i DevOps platforma Azure oferuje szeroką gamę usług platformy jako usługi (PaaS), które są przydatne do ASP.NET Core deweloperów.</span><span class="sxs-lookup"><span data-stu-id="f4bc7-107">Beyond web hosting and DevOps, Azure has a wide array of Platform-as-a-Service (PaaS) services useful to ASP.NET Core developers.</span></span> <span data-ttu-id="f4bc7-108">Ta sekcja zawiera krótkie omówienie niektórych z najczęściej używanych usług.</span><span class="sxs-lookup"><span data-stu-id="f4bc7-108">This section gives a brief overview of some of the most commonly used services.</span></span>

## <a name="storage-and-databases"></a><span data-ttu-id="f4bc7-109">Magazyn i bazy danych</span><span class="sxs-lookup"><span data-stu-id="f4bc7-109">Storage and databases</span></span>

<span data-ttu-id="f4bc7-110">[Redis Cache](/azure/redis-cache/) to buforowanie danych o wysokiej przepływności i małych opóźnieniach dostępne jako usługa.</span><span class="sxs-lookup"><span data-stu-id="f4bc7-110">[Redis Cache](/azure/redis-cache/) is high-throughput, low-latency data caching available as a service.</span></span> <span data-ttu-id="f4bc7-111">Może służyć do buforowania danych wyjściowych stron, zmniejszania żądań bazy danych i zapewniania ASP.NET Core stanu sesji w wielu wystąpieniach aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f4bc7-111">It can be used for caching page output, reducing database requests, and providing ASP.NET Core session state across multiple instances of an app.</span></span>

<span data-ttu-id="f4bc7-112">[Usługa Azure Storage](/azure/storage/) to wysoce skalowalny magazyn w chmurze na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="f4bc7-112">[Azure Storage](/azure/storage/) is Azure's massively scalable cloud storage.</span></span> <span data-ttu-id="f4bc7-113">Deweloperzy mogą korzystać z [queue storage](/azure/storage/queues/storage-queues-introduction) dla niezawodnej usługi kolejkowania komunikatów, a [Table Storage](/azure/storage/tables/table-storage-overview) to NoSQL klucz-wartość magazynu, który umożliwia szybkie tworzenie aplikacji przy użyciu ogromnych zestawów danych z częściową strukturą.</span><span class="sxs-lookup"><span data-stu-id="f4bc7-113">Developers can take advantage of [Queue Storage](/azure/storage/queues/storage-queues-introduction) for reliable message queuing, and [Table Storage](/azure/storage/tables/table-storage-overview) is a NoSQL key-value store designed for rapid development using massive, semi-structured data sets.</span></span>

<span data-ttu-id="f4bc7-114">[Azure SQL Database](/azure/sql-database/) zapewnia znane funkcje relacyjnej bazy danych jako usługi korzystające z aparatu Microsoft SQL Server.</span><span class="sxs-lookup"><span data-stu-id="f4bc7-114">[Azure SQL Database](/azure/sql-database/) provides familiar relational database functionality as a service using the Microsoft SQL Server Engine.</span></span>

<span data-ttu-id="f4bc7-115">[Cosmos DB](/azure/cosmos-db/) globalnie dystrybuowana, wielomodelowa usługa bazy danych NoSQL.</span><span class="sxs-lookup"><span data-stu-id="f4bc7-115">[Cosmos DB](/azure/cosmos-db/) globally distributed, multi-model NoSQL database service.</span></span> <span data-ttu-id="f4bc7-116">Dostępnych jest wiele interfejsów API, w tym interfejs API SQL (dawniej nazywany DocumentDB), Cassandra i MongoDB.</span><span class="sxs-lookup"><span data-stu-id="f4bc7-116">Multiple APIs are available, including SQL API (formerly called DocumentDB), Cassandra, and MongoDB.</span></span>

## :::no-loc(Identity):::

<span data-ttu-id="f4bc7-117">[Azure Active Directory](/azure/active-directory/) i [Azure Active Directory B2C](/azure/active-directory-b2c/) są usługami tożsamości.</span><span class="sxs-lookup"><span data-stu-id="f4bc7-117">[Azure Active Directory](/azure/active-directory/) and [Azure Active Directory B2C](/azure/active-directory-b2c/) are both identity services.</span></span> <span data-ttu-id="f4bc7-118">Azure Active Directory jest zaprojektowana dla scenariuszy korporacyjnych i umożliwia współpracę z usługą Azure AD B2B (Business-to-Business), natomiast Azure Active Directory B2C jest zamierzonymi scenariuszami biznesowymi, w tym logowaniem do sieci społecznościowej.</span><span class="sxs-lookup"><span data-stu-id="f4bc7-118">Azure Active Directory is designed for enterprise scenarios and enables Azure AD B2B (business-to-business) collaboration, while Azure Active Directory B2C is intended business-to-customer scenarios, including social network sign-in.</span></span>

## <a name="mobile"></a><span data-ttu-id="f4bc7-119">Aplikacje mobilne</span><span class="sxs-lookup"><span data-stu-id="f4bc7-119">Mobile</span></span>

<span data-ttu-id="f4bc7-120">[Notification Hubs](/azure/notification-hubs/) to wieloplatformowy, Skalowalny aparat powiadomień wypychanych umożliwiający szybkie wysyłanie milionów komunikatów do aplikacji działających na różnych typach urządzeń.</span><span class="sxs-lookup"><span data-stu-id="f4bc7-120">[Notification Hubs](/azure/notification-hubs/) is a multi-platform, scalable push-notification engine to quickly send millions of messages to apps running on various types of devices.</span></span>

## <a name="web-infrastructure"></a><span data-ttu-id="f4bc7-121">Infrastruktura sieci Web</span><span class="sxs-lookup"><span data-stu-id="f4bc7-121">Web infrastructure</span></span>

<span data-ttu-id="f4bc7-122">[Azure Container Service](/azure/aks/) zarządza hostowanym środowiskiem Kubernetes, dzięki czemu można szybko i łatwo wdrażać aplikacje z kontenerami bez wiedzy z zakresu aranżacji kontenerów i zarządzać nimi.</span><span class="sxs-lookup"><span data-stu-id="f4bc7-122">[Azure Container Service](/azure/aks/) manages your hosted Kubernetes environment, making it quick and easy to deploy and manage containerized apps without container orchestration expertise.</span></span>

<span data-ttu-id="f4bc7-123">[Azure Search](/azure/search/) służy do tworzenia rozwiązania wyszukiwania dla przedsiębiorstw za pośrednictwem prywatnej, heterogenicznych zawartości.</span><span class="sxs-lookup"><span data-stu-id="f4bc7-123">[Azure Search](/azure/search/) is used to create an enterprise search solution over private, heterogenous content.</span></span>

<span data-ttu-id="f4bc7-124">[Service Fabric](/azure/service-fabric/) to platforma systemów rozproszonych ułatwiająca pakowanie i wdrażanie skalowalnych i niezawodnych mikrousług i kontenerów oraz zarządzanie nimi.</span><span class="sxs-lookup"><span data-stu-id="f4bc7-124">[Service Fabric](/azure/service-fabric/) is a distributed systems platform that makes it easy to package, deploy, and manage scalable and reliable microservices and containers.</span></span>
