![Microsoft Cloud Workshops](https://raw.githubusercontent.com/Microsoft/MCW-Template-Cloud-Workshop/main/Media/ms-cloud-workshop.png "Microsoft Cloud Workshops")

<div class="MCWHeader1">
AI-led business process automation
</div>

<div class="MCWHeader2">
Hands-on lab step-by-step
</div>

<div class="MCWHeader3">
July 2021
</div>

Information in this document, including URL and other Internet Web site references, is subject to change without notice. Unless otherwise noted, the example companies, organizations, products, domain names, e-mail addresses, logos, people, places, and events depicted herein are fictitious, and no association with any real company, organization, product, domain name, e-mail address, logo, person, place or event is intended or should be inferred. Complying with all applicable copyright laws is the responsibility of the user. Without limiting the rights under copyright, no part of this document may be reproduced, stored in or introduced into a retrieval system, or transmitted in any form or by any means (electronic, mechanical, photocopying, recording, or otherwise), or for any purpose, without the express written permission of Microsoft Corporation.

Microsoft may have patents, patent applications, trademarks, copyrights, or other intellectual property rights covering subject matter in this document. Except as expressly provided in any written license agreement from Microsoft, the furnishing of this document does not give you any license to these patents, trademarks, copyrights, or other intellectual property.

The names of manufacturers, products, or URLs are provided for informational purposes only and Microsoft makes no representations and warranties, either expressed, implied, or statutory, regarding these manufacturers or the use of the products with any Microsoft technologies. The inclusion of a manufacturer or product does not imply endorsement of Microsoft of the manufacturer or product. Links may be provided to third party sites. Such sites are not under the control of Microsoft and Microsoft is not responsible for the contents of any linked site or any link contained in a linked site, or any changes or updates to such sites. Microsoft is not responsible for webcasting or any other form of transmission received from any linked site. Microsoft is providing these links to you only as a convenience, and the inclusion of any link does not imply endorsement of Microsoft of the site or the products contained therein.

© 2021 Microsoft Corporation. All rights reserved.

Microsoft and the trademarks listed at <https://www.microsoft.com/legal/intellectualproperty/Trademarks/Usage/General.aspx> are trademarks of the Microsoft group of companies. All other trademarks are property of their respective owners.
# AI-led business process automation hands-on lab step-by-step

## Abstract and learning objectives

In this hands-on lab, you will learn to train a Form Recognizer model to extract data from images of documents and use Speech services to transcribe and translate audio. You will also learn to analyze transcribed text with Healthcare text analytics to extract medical terminology, medication dosages, and diagnoses.

At the end of this hands-on lab, you will be better able to implement a business process automation solution that leverages Azure Cognitive Services.

## Overview

Contoso Healthcare is a major hospital network consisting of multiple locations across the United States. One of Contoso Healthcare's most significant needs is to have the ability to process handwritten and electronically filled medical claims forms. Each hospital uploads claims forms to the central Azure Storage as a standard. Currently, claims forms are completed as both digital files and physical paper documents. Employees then review each document and enter data manually into the claims system. Contoso Healthcare is looking to automate the business process of extracting claims form data to reduce overall form processing time, data-entry errors, and the loss of physical documents. Contoso can also then re-direct their employees to more impactful tasks and increase overall productivity.

In addition to medical claims form processing, Contoso is looking to automate the process of transcribing, translating, and storing patient/doctor visit audio recordings. Currently, each hospital records audio files of patient/physician visits. This data is archived in a central Azure Storage account. The recordings are used strictly as an auditing tool should the details of any visit be questioned. When the results of a patient visit are challenged, the recording of the visit is retrieved and audibly reviewed by hospital employees. Unfortunately, this manual review process is not standard across the hospital network. As a result, each hospital has its methods of dealing with patient audio file storage, retrieval, and review. A translation may also be needed in addition to patient audio transcription when the visit language is Spanish. Currently, multiple language interpreters need to be on-hand at each hospital for the manual audio review process.

Contoso Healthcare wants to implement useful reporting visualizations over the extracted claims processing data, such as visualizing the ratio of total cost and the amount covered for a patient. Doctors are also interested in extracting critical insights from the patient visit audio transcriptions, preferably via search functionality available on their internal portal site.

## Solution architecture

Below is a high-level architecture diagram of the solution you implement in this hands-on lab. Please review this carefully to understand the whole of the solution as you are working on the various components.

![This solution diagram includes a high-level overview of the architecture implemented within this hands-on lab.](media/architecture-diagram.png "Solution architecture diagram")

> **Note:** The solution provided is only one of many possible, viable approaches.

Hospitals in the Contoso Healthcare network provide PDF files of claim forms and WAV files of visit audio recordings via blobs in an Azure Storage account. Two event grid subscriptions propagate the blob creation events that trigger two separate functions in a [Function App](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal).

One of the functions handles PDF processing. The function uses an [Azure Forms Recognizer](https://azure.microsoft.com/services/form-recognizer/) that has a custom trainer model to extract the required information from forms. Once the metadata is extracted, the function saves the result in [Azure CosmosDB](https://azure.microsoft.com/services/cosmos-db/), allowing Contoso to build custom PowerBI reports with a direct query connection. Additionally, the data is indexed in an [Azure Cognitive Search](https://azure.microsoft.com/services/search/) to be served in a unified search experience on the internal hospital portal.

A second function in the Function App processes audio recordings. Contoso uses [Azure Cognitive Speech Audio Language Identification](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-automatic-language-detection?pivots=programming-language-csharp) to detect the language of the audio file and transcribe it to text. Once the text transcriptions are ready, Spanish transcriptions are translated to English using [Azure Cognitive Services Text Translator](https://azure.microsoft.com/services/cognitive-services/translator/). Finally, [Azure Cognitive Services Text Analytics for Health](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-for-health?tabs=ner) is used to extract and label relevant medical information to provide a richer search experience in the internal hospital portal. Once the results are ready, the function saves the data in an Azure CosmosDB collection to be indexed by Azure Cognitive Search.

Finally, the internal hospital portal queries the indexes created in Azure Cognitive Search, offering a unified search experience for both structured and unstructured data sets.

## Requirements

- Microsoft Azure subscription must be pay-as-you-go or MSDN.
  - Trial subscriptions will _not_ work.

## Before the hands-on lab

Refer to the Before the hands-on lab setup guide before continuing to the lab exercises.