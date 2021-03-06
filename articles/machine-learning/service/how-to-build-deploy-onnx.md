---
title: ONNX et Azure Machine Learning | Créer et déployer des modèles
description: Découvrez ONNX et comment utiliser Azure Machine Learning pour créer et déployer des modèles ONNX.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: prasantp
author: prasanthpul
ms.date: 09/24/2018
ms.openlocfilehash: 190b7fff24c9d6b3dee86471b56ad68c962e51ce
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2018
ms.locfileid: "49116876"
---
# <a name="onnx-and-azure-machine-learning-create-and-deploy-interoperable-ai-models"></a>ONNX et Azure Machine Learning : Créer et déployer des modèles d’intelligence artificielle interopérables

Le format [ONNX (Open Neural Network Exchange)](http://onnx.ai) est une norme ouverte de représentation des modèles Machine Learning. ONNX est pris en charge par une [communauté de partenaires](http://onnx.ai/supported-tools), notamment Microsoft, qui créent des outils et des frameworks compatibles. Microsoft s’est engagé à promouvoir une intelligence artificielle ouverte et interopérable afin de permettre aux scientifiques des données et aux développeurs de :

+ Utiliser le framework de leur choix pour créer et entraîner des modèles.
+ Déployer des modèles multiplateformes avec un travail d’intégration minimal

Microsoft prend en charge ONNX dans toute sa gamme de produits, notamment Azure et Windows, pour vous aider à atteindre ces objectifs.  

## <a name="why-choose-onnx"></a>Pourquoi choisir ONNX ?
L’interopérabilité que vous obtenez avec ONNX permet d’accélérer la mise en production de vos idées. Avec ONNX, les scientifiques des données peuvent choisir leur framework préféré pour effectuer leur travail. De même, les développeurs consacreront moins de temps à préparer les modèles pour la production et à déployer sur le cloud et la périphérie.  

Vous pouvez créer des modèles ONNX à partir de nombreux frameworks, à savoir PyTorch, Chainer, Microsoft Cognitive Toolkit (CNTK), MXNet, ML.Net, TensorFlow, Keras, SciKit-Learn, etc.

Il existe également un écosystème d’outils de visualisation et d’accélération des modèles ONNX. Plusieurs modèles ONNX préentraînés sont également disponibles pour les scénarios courants.

[Les modèles ONNX peuvent être déployés](#deploy) sur le cloud à l’aide d’Azure Machine Learning et du runtime ONNX. Ils peuvent également être déployés sur des appareils Windows 10 à l’aide de [Windows ML](https://docs.microsoft.com/windows/ai/). Ils peuvent même être déployés sur d’autres plateformes, grâce aux convertisseurs mis à disposition par la communauté ONNX. 

[ ![Organigramme ONNX montrant l’entraînement, les convertisseurs et le déploiement](media/concept-onnx/onnx.png) ] (./media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>Obtenir des modèles ONNX

Vous pouvez obtenir des modèles ONNX de plusieurs façons :
+ Obtenir un modèle ONNX préentraîné à partir de la collection [ONNX Model Zoo](https://github.com/onnx/models) (voir l’exemple au bas de cet article)
+ Générer un modèle ONNX personnalisé à partir du [service Vision personnalisée Azure](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/) 
+ Convertir le modèle existant d’un autre format vers le format ONNX (voir l’exemple au bas de cet article) 
+ Entraîner un nouveau modèle ONNX dans le service Azure Machine Learning (voir l’exemple au bas de cet article)

## <a name="saveconvert-your-models-to-onnx"></a>Enregistrer/convertir vos modèles au format ONNX

Vous pouvez convertir des modèles existants au format ONNX ou les enregistrer au format ONNX à la fin de votre entraînement.

|Infrastructure pour le modèle|Exemple ou outil de conversion|
|-----|-------|
|PyTorch|[Bloc-notes Jupyter](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb)|
|Microsoft&nbsp;Cognitive&nbsp;Toolkit&nbsp;(CNTK)|[Bloc-notes Jupyter](https://github.com/onnx/tutorials/blob/master/tutorials/CntkOnnxExport.ipynb)|
|TensorFlow|[tensorflow-onnx converter](https://github.com/onnx/tensorflow-onnx)|
|Chainer|[Bloc-notes Jupyter](https://github.com/onnx/tutorials/blob/master/tutorials/ChainerOnnxExport.ipynb)|
|MXNet|[Bloc-notes Jupyter](https://github.com/onnx/tutorials/blob/master/tutorials/MXNetONNXExport.ipynb)|
|Keras, ScitKit-Learn, CoreML<br/>XGBoost, et libSVM|[WinMLTools](https://docs.microsoft.com/windows/ai/convert-model-winmltools)|

Vous trouverez la liste la plus récente des frameworks et des convertisseurs pris en charge sur le [site des tutoriels ONNX](https://github.com/onnx/tutorials).

<a name="deploy"></a>

## <a name="deploy-onnx-models-in-azure"></a>Déployer des modèles ONNX dans Azure

Avec le service Azure Machine Learning, vous pouvez déployer, gérer et superviser vos modèles ONNX. À l’aide du [workflow de déploiement](concept-model-management-and-deployment.md) standard et du runtime ONNX, vous pouvez créer un point de terminaison REST hébergé dans le cloud. Pour essayer par vous-même, consultez l’exemple complet de bloc-notes Jupyter fourni à la fin de cet article. 

### <a name="install-and-configure-the-onnx-runtime"></a>Installer et configurer le runtime ONNX

Le runtime ONNX est un moteur d’inférence à hautes performances pour les modèles ONNX. Il est fourni avec une API Python et procure une accélération matérielle sur UC et GPU. Actuellement, il prend en charge les modèles ONNX 1.2 et s’exécute sur Linux Ubuntu 16.04. Les deux packages pour [UC](https://pypi.org/project/onnxruntime) et [GPU](https://pypi.org/project/onnxruntime-gpu) sont disponibles sur [PyPi.org](https://pypi.org).

Pour installer le runtime ONNX, utilisez :
```python
pip install onnxruntime
```

Pour appeler le runtime ONNX dans votre script Python, utilisez :
```python
import onnxruntime

session = onnxruntime.InferenceSession("path to model")
```

La documentation qui accompagne le modèle indique généralement les entrées et sorties pour l’utilisation du modèle. Vous pouvez également utiliser un outil de visualisation tel que [Netron](https://github.com/lutzroeder/Netron) pour afficher le modèle. Le runtime ONNX vous permet également d’interroger les métadonnées, entrées et sorties du modèle :
```python
session.get_modelmeta()
first_input_name = session.get_inputs()[0].name
first_output_name = session.get_outputs()[0].name
```

Pour déduire votre modèle, utilisez `run` et passez la liste des sorties à retourner (laissez vide si vous souhaitez toutes les retourner) et un mappage des valeurs d’entrée. Le résultat est une liste des sorties.
```python
results = session.run(["output1", "output2"], {"input1": indata1, "input2": indata2})
results = session.run([], {"input1": indata1, "input2": indata2})
```

Pour obtenir des informations de référence complètes sur l’API, consultez [les documents de référence sur le runtime ONNX](https://aka.ms/onnxruntime-python).

### <a name="example-deployment-steps"></a>Exemples d’étapes de déploiement

Voici un exemple de déploiement d’un modèle ONNX :

1. Initialisez votre espace de travail du service Azure Machine Learning. Si vous n’en avez pas encore, découvrez comment créer un espace de travail dans [ce guide de démarrage rapide](quickstart-get-started.md).

   ```python
   from azureml.core import Workspace

   ws = Workspace.from_config()
   print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep = '\n')
   ```

2. Inscrivez le modèle auprès d’Azure Machine Learning.

   ```python
   from azureml.core.model import Model

   model = Model.register(model_path = "model.onnx",
                          model_name = "MyONNXmodel",
                          tags = ["onnx"],
                          description = "test",
                          workspace = ws)
   ```

3. Créez une image avec le modèle et les dépendances.

   ```python
   from azureml.core.image import ContainerImage
   
   image_config = ContainerImage.image_configuration(execution_script = "score.py",
                                                     runtime = "python",
                                                     conda_file = "myenv.yml",
                                                     description = "test",
                                                     tags = ["onnx"]
                                                    )

   image = ContainerImage.create(name = "myonnxmodelimage",
                                 # this is the model object
                                 models = [model],
                                 image_config = image_config,
                                 workspace = ws)

   image.wait_for_creation(show_output = True)
   ```

   Le fichier `score.py` contient la logique de scoring ; il doit être inclus dans l’image. Ce fichier est utilisé pour exécuter le modèle dans l’image. Consultez ce [tutoriel](tutorial-deploy-models-with-aml.md#create-scoring-script) pour obtenir des instructions sur la création d’un script de scoring. Vous trouverez ci-dessous un exemple de fichier pour un modèle ONNX :

   ```python
   import onnxruntime
   import json
   import numpy as np
   import sys
   from azureml.core.model import Model

   def init():
       global model_path
       model_path = Model.get_model_path(model_name = 'MyONNXmodel')

   def run(raw_data):
       try:
           data = json.loads(raw_data)['data']
           data = np.array(data)
        
           sess = onnxruntime.InferenceSession(model_path)
           result = sess.run(["outY"], {"inX": data})
        
           return json.dumps({"result": result.tolist()})
       except Exception as e:
           result = str(e)
           return json.dumps({"error": result})
   ```

   Le fichier `myenv.yml` décrit les dépendances nécessaires pour l’image. Consultez ce [tutoriel](tutorial-deploy-models-with-aml.md#create-environment-file) pour obtenir des instructions sur la façon de créer un fichier d’environnement, tel que cet exemple de fichier :

   ```python
   from azureml.core.conda_dependencies import CondaDependencies 

   myenv = CondaDependencies()
   myenv.add_pip_package("numpy")
   myenv.add_pip_package("azureml-core")
   myenv.add_pip_package("onnxruntime")

   with open("myenv.yml","w") as f:
    f.write(myenv.serialize_to_string())
   ```

4. Déployez votre modèle ONNX avec Azure Machine Learning sur :
   + Azure Container Instances (ACI) : [Découvrez comment...](how-to-deploy-to-aci.md)

   + Azure Kubernetes Service (AKS) : [Découvrez comment...](how-to-deploy-to-aks.md)


## <a name="examples"></a>Exemples
 
Les notebooks suivants montrent comment créer des modèles ONNX et les déployer avec Azure Machine Learning : 
+ `/onnx/onnx-modelzoo-aml-deploy-resnet50.ipynb` 
+ `/onnx/onnx-convert-aml-deploy-tinyyolo.ipynb`
+ `/onnx/onnx-train-pytorch-aml-deploy-mnist.ipynb`

Les notebooks suivants montrent comment déployer des modèles ONNX existants avec Azure Machine Learning : 
+ [onnx/onnx-inference-mnist.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/onnx/onnx-inference-mnist.ipynb) 
+ [onnx/onnx-inference-emotion-recognition.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/onnx/onnx-inference-emotion-recognition.ipynb)
 
Consultez ces notebooks :
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="more-info"></a>En savoir plus

Apprenez-en davantage sur ONNX ou contribuez au projet :
+ [Site web du projet ONNX](http://onnx.ai)

+ [Code ONNX sur GitHub](https://github.com/onnx/onnx)
