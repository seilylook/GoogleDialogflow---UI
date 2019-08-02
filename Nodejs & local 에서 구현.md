How to Implement
================

1. 기존에 존재한 dialogflow 의 프로젝트를 선택하거나, 새로운 프로젝트를 dialogflow 에 생성한다. 

  
  <img width="925" alt="스크린샷 2019-08-02 오후 3 07 08" src="https://user-images.githubusercontent.com/48667219/62348235-64c56e80-b537-11e9-977e-b2e2071671dd.png">


2. dialogflow 의 setting 에 들어가 'project ID' 를 클릭한다. 

  <img width="918" alt="스크린샷 2019-08-02 오후 3 10 48" src="https://user-images.githubusercontent.com/48667219/62348488-c685d880-b537-11e9-8534-1def1af19698.png">

  -> 클릭할 시, 'google cloud platform' 으로 이동한다. 
  -> 만약 기존에 다른 프로젝트를 진행하는데, billing을 설정해 놓았다면, 자동으로 enable 될 것이다. 기존에 진행했던 프로젝트가 존재하지 않는다면, 새롭게            billing 을 해주어야 한다. 
  
  
3. Dialogflow API 를 enalble 시켜 주어야 한다. 

  <img width="1680" alt="스크린샷 2019-08-02 오후 3 20 42" src="https://user-images.githubusercontent.com/48667219/62348994-62fcaa80-b539-11e9-88e5-5e2a3108d74b.png">
  
  <img width="763" alt="스크린샷 2019-08-02 오후 3 23 21" src="https://user-images.githubusercontent.com/48667219/62349068-8e7f9500-b539-11e9-95a2-cc84eb4ea29b.png">
  
  <img width="759" alt="스크린샷 2019-08-02 오후 3 24 33" src="https://user-images.githubusercontent.com/48667219/62349142-d8687b00-b539-11e9-9266-e27a75739eec.png">


  -> 해당 작업을 하는 이유는, local 의 node js 에서 google dialogflow API library 를 사용할 때 사용허가를 해주기 위해서다.
  -> 이 작업을 해주지 않는다면, permission error 가 발생하여 API 를 사용할 수 없다. 
  
  
4. https://cloud.google.com/docs/authentication/getting-started 으로 이동하여 API 접근을 위한 key 값을 생성하고 이를 path 환경 설정을 통해
   설정해 주어야 한다. 
   
   * 이에 대한 작업은 https://cloud.google.com/storage/docs/reference/libraries 으로 이동한다. 
   
      <img width="879" alt="스크린샷 2019-08-02 오후 3 40 01" src="https://user-images.githubusercontent.com/48667219/62349884-f636df80-b53b-11e9-987a-2008d58a1c9a.png">

      -> json 파일을 다운로드 해주었다면 환경 변수를 설정해 주어야 한다. 
      
      ~~~
      export GOOGLE_APPLICATION_CREDENTIALS = "파일 위치" 
      ~~~
      
      -> 주의할 점: 환경 변수 설정은 현제 terminal 및 shell 세션에만 적용되므로 새로운 섹션을 열었을 경우 변수를 다시 설정해야 한다.
      
      
5. 이제 nodejs 를 이용하여 코드 작업을 수행한다
 
  가. 먼저 Node js 작업 환경을 설정해준다. 
  
    
    npm init
    
    npm install express
    
    npm install --save dialogflow
    
    
    
  나. 해당 코드는 google 에서 제공하는 nodejs 환경에서 google dialogflow API 를 사용할 수 있는 코드이다. 
  
    
        async function createEntityType(projectId, displayName, kind) {
        // [START dialogflow_create_entity_type]
        // Imports the Dialogflow library
        const dialogflow = require('dialogflow');

        // Instantiates clients
        const entityTypesClient = new dialogflow.EntityTypesClient();

        // The path to the agent the created entity type belongs to.
        const agentPath = entityTypesClient.projectAgentPath(projectId);

        const createEntityTypeRequest = {
          parent: agentPath,
          entityType: {
            displayName: displayName,
            kind: kind,
          },
        };

        const responses = await entityTypesClient.createEntityType(
          createEntityTypeRequest
        );
        console.log(`Created ${responses[0].name} entity type`);
        // [END dialogflow_create_entity_type]
      }

      async function listEntityTypes(projectId) {
        // [START dialogflow_list_entity_types]
        // Imports the Dialogflow library
        const dialogflow = require('dialogflow');

        // Instantiates clients
        const entityTypesClient = new dialogflow.EntityTypesClient();

        // The path to the agent the entity types belong to.
        const agentPath = entityTypesClient.projectAgentPath(projectId);

        const request = {
          parent: agentPath,
        };

        // Call the client library to retrieve a list of all existing entity types.
        const [response] = await entityTypesClient.listEntityTypes(request);
        response.forEach(entityType => {
          console.log(`Entity type name: ${entityType.name}`);
          console.log(`Entity type display name: ${entityType.displayName}`);
          console.log(`Number of entities: ${entityType.entities.length}\n`);
        });
        return response;
        // [END dialogflow_list_entity_types]
      }

      async function deleteEntityType(projectId, entityTypeId) {
        // [START dialogflow_delete_entity_type]
        // Imports the Dialogflow library
        const dialogflow = require('dialogflow');

        // Instantiates clients
        const entityTypesClient = new dialogflow.EntityTypesClient();

        const entityTypePath = entityTypesClient.entityTypePath(
          projectId,
          entityTypeId
        );

        const request = {
          name: entityTypePath,
        };

        // Call the client library to delete the entity type.
        const response = await entityTypesClient.deleteEntityType(request);
        console.log(`Entity type ${entityTypePath} deleted`);
        return response;
        // [END dialogflow_delete_entity_type]
      }

      // /////////////////////////////////////////////////////////////////////////////
      // Operations for entities.
      // /////////////////////////////////////////////////////////////////////////////

      async function createEntity(projectId, entityTypeId, entityValue, synonyms) {
        // [START dialogflow_create_entity]
        // Imports the Dialogflow library
        const dialogflow = require('dialogflow');

        // Instantiates clients
        const entityTypesClient = new dialogflow.EntityTypesClient();

        // The path to the agent the created entity belongs to.
        const agentPath = entityTypesClient.entityTypePath(projectId, entityTypeId);

        const entity = {
          value: entityValue,
          synonyms: synonyms,
        };

        const createEntitiesRequest = {
          parent: agentPath,
          entities: [entity],
        };

        const [response] = await entityTypesClient.batchCreateEntities(
          createEntitiesRequest
        );
        console.log('Created entity type:');
        console.log(response);
        // [END dialogflow_create_entity]
      }

      async function listEntities(projectId, entityTypeId) {
        // [START dialogflow_create_entity]
        // Imports the Dialogflow library
        const dialogflow = require('dialogflow');

        // Instantiates clients
        const entityTypesClient = new dialogflow.EntityTypesClient();

        // The path to the agent the entity types belong to.
        const entityTypePath = entityTypesClient.entityTypePath(
          projectId,
          entityTypeId
        );

        // The request.
        const request = {
          name: entityTypePath,
        };

        // Call the client library to retrieve a list of all existing entity types.
        const [response] = await entityTypesClient.getEntityType(request);
        response.entities.forEach(entity => {
          console.log(`Entity value: ${entity.value}`);
          console.log(`Entity synonyms: ${entity.synonyms}`);
        });
        return response;
        // [END dialogflow_create_entity]
      }

      async function deleteEntity(projectId, entityTypeId, entityValue) {
        // [START dialogflow_delete_entity]
        // Imports the Dialogflow library
        const dialogflow = require('dialogflow');

        // Instantiates clients
        const entityTypesClient = new dialogflow.EntityTypesClient();

        // The path to the agent the entity types belong to.
        const entityTypePath = entityTypesClient.entityTypePath(
          projectId,
          entityTypeId
        );

        const request = {
          parent: entityTypePath,
          entityValues: [entityValue],
        };

        // Call the client library to delete the entity type.
        await entityTypesClient.batchDeleteEntities(request);
        console.log(`Entity Value ${entityValue} deleted`);
        // [END dialogflow_delete_entity]
      }

      // /////////////////////////////////////////////////////////////////////////////
      // Operations for intents
      // /////////////////////////////////////////////////////////////////////////////

      async function listIntents(projectId) {
        // [START dialogflow_list_intents]
        // Imports the Dialogflow library
        const dialogflow = require('dialogflow');

        // Instantiates clients
        const intentsClient = new dialogflow.IntentsClient();

        // The path to identify the agent that owns the intents.
        const projectAgentPath = intentsClient.projectAgentPath(projectId);

        const request = {
          parent: projectAgentPath,
        };

        console.log(projectAgentPath);

        // Send the request for listing intents.
        const [response] = await intentsClient.listIntents(request);
        response.forEach(intent => {
          console.log('====================');
          console.log(`Intent name: ${intent.name}`);
          console.log(`Intent display name: ${intent.displayName}`);
          console.log(`Action: ${intent.action}`);
          console.log(`Root folowup intent: ${intent.rootFollowupIntentName}`);
          console.log(`Parent followup intent: ${intent.parentFollowupIntentName}`);

          console.log('Input contexts:');
          intent.inputContextNames.forEach(inputContextName => {
            console.log(`\tName: ${inputContextName}`);
          });

          console.log('Output contexts:');
          intent.outputContexts.forEach(outputContext => {
            console.log(`\tName: ${outputContext.name}`);
          });
        });
        // [END dialogflow_list_intents]
      }

      async function createIntent(
        projectId,
        displayName,
        trainingPhrasesParts,
        messageTexts
      ) {
        // [START dialogflow_create_intent]
        // Imports the Dialogflow library
        const dialogflow = require('dialogflow');

        // Instantiates the Intent Client
        const intentsClient = new dialogflow.IntentsClient();

        // The path to identify the agent that owns the created intent.
        const agentPath = intentsClient.projectAgentPath(projectId);

        const trainingPhrases = [];

        trainingPhrasesParts.forEach(trainingPhrasesPart => {
          const part = {
            text: trainingPhrasesPart,
          };

          // Here we create a new training phrase for each provided part.
          const trainingPhrase = {
            type: 'EXAMPLE',
            parts: [part],
          };

          trainingPhrases.push(trainingPhrase);
        });

        const messageText = {
          text: messageTexts,
        };

        const message = {
          text: messageText,
        };

        const intent = {
          displayName: displayName,
          trainingPhrases: trainingPhrases,
          messages: [message],
        };

        const createIntentRequest = {
          parent: agentPath,
          intent: intent,
        };

        // Create the intent
        const responses = await intentsClient.createIntent(createIntentRequest);
        console.log(`Intent ${responses[0].name} created`);
        // [END dialogflow_create_intent]
      }

      async function deleteIntent(projectId, intentId) {
        // [START dialogflow_delete_intent]
        // Imports the Dialogflow library
        const dialogflow = require('dialogflow');

        // Instantiates clients
        const intentsClient = new dialogflow.IntentsClient();

        const intentPath = intentsClient.intentPath(projectId, intentId);

        const request = {name: intentPath};

        // Send the request for deleting the intent.
        const result = await intentsClient.deleteIntent(request);
        console.log(`Intent ${intentPath} deleted`);
        return result;
        // [END dialogflow_delete_intent]
      }

      // /////////////////////////////////////////////////////////////////////////////
      // Operations for contexts
      // /////////////////////////////////////////////////////////////////////////////

      async function createContext(projectId, sessionId, contextId, lifespanCount) {
        // [START dialogflow_create_context]
        // Imports the Dialogflow library
        const dialogflow = require('dialogflow');

        // Instantiates clients
        const contextsClient = new dialogflow.ContextsClient();

        const sessionPath = contextsClient.sessionPath(projectId, sessionId);
        const contextPath = contextsClient.contextPath(
          projectId,
          sessionId,
          contextId
        );

        const createContextRequest = {
          parent: sessionPath,
          context: {
            name: contextPath,
            lifespanCount: lifespanCount,
          },
        };

        const responses = await contextsClient.createContext(createContextRequest);
        console.log(`Created ${responses[0].name} context`);
        // [END dialogflow_create_context]
      }

      async function listContexts(projectId, sessionId) {
        // [START dialogflow_list_contexts]
        // Imports the Dialogflow library
        const dialogflow = require('dialogflow');

        // Instantiates clients
        const contextsClient = new dialogflow.ContextsClient();

        // The path to identify the agent that owns the contexts.
        const sessionPath = contextsClient.sessionPath(projectId, sessionId);

        const request = {
          parent: sessionPath,
        };

        // Send the request for listing contexts.
        const [response] = await contextsClient.listContexts(request);
        response.forEach(context => {
          console.log(`Context name: ${context.name}`);
          console.log(`Lifespan count: ${context.lifespanCount}`);
          console.log('Fields:');
          if (context.parameters !== null) {
            context.parameters.fields.forEach(field => {
              console.log(`\t${field.field}: ${field.value}`);
            });
          }
        });
        return response;
        // [END dialogflow_list_contexts]
      }

      async function deleteContext(projectId, sessionId, contextId) {
        // [START dialogflow_delete_context]
        // Imports the Dialogflow library
        const dialogflow = require('dialogflow');

        // Instantiates clients
        const contextsClient = new dialogflow.ContextsClient();

        const contextPath = contextsClient.contextPath(
          projectId,
          sessionId,
          contextId
        );

        const request = {
          name: contextPath,
        };

        // Send the request for retrieving the context.
        const result = await contextsClient.deleteContext(request);
        console.log(`Context ${contextPath} deleted`);
        return result;
        // [END dialogflow_delete_context]
      }

      // /////////////////////////////////////////////////////////////////////////////
      // Operations for session entity type
      // /////////////////////////////////////////////////////////////////////////////

      async function createSessionEntityType(
        projectId,
        sessionId,
        entityValues,
        entityTypeDisplayName,
        entityOverrideMode
      ) {
        // Imports the Dialogflow library
        const dialogflow = require('dialogflow');

        // Instantiates clients
        const sessionEntityTypesClient = new dialogflow.SessionEntityTypesClient();

        const sessionPath = sessionEntityTypesClient.sessionPath(
          projectId,
          sessionId
        );
        const sessionEntityTypePath = sessionEntityTypesClient.sessionEntityTypePath(
          projectId,
          sessionId,
          entityTypeDisplayName
        );

        // Here we use the entity value as the only synonym.
        const entities = [];
        entityValues.forEach(entityValue => {
          entities.push({
            value: entityValue,
            synonyms: [entityValue],
          });
        });

        const sessionEntityTypeRequest = {
          parent: sessionPath,
          sessionEntityType: {
            name: sessionEntityTypePath,
            entityOverrideMode: entityOverrideMode,
            entities: entities,
          },
        };

        const [response] = await sessionEntityTypesClient.createSessionEntityType(
          sessionEntityTypeRequest
        );
        console.log('SessionEntityType created:');
        console.log(response);
      }

      async function listSessionEntityTypes(projectId, sessionId) {
        // Imports the Dialogflow library
        const dialogflow = require('dialogflow');

        // Instantiates clients
        const sessionEntityTypesClient = new dialogflow.SessionEntityTypesClient();
        const sessionPath = sessionEntityTypesClient.sessionPath(
          projectId,
          sessionId
        );

        const request = {
          parent: sessionPath,
        };

        // Send the request for retrieving the sessionEntityType.
        const [response] = await sessionEntityTypesClient.listSessionEntityTypes(
          request
        );
        response.forEach(sessionEntityType => {
          console.log(`Session entity type name: ${sessionEntityType.name}`);
          console.log(`Number of entities: ${sessionEntityType.entities.length}\n`);
        });
      }

      async function deleteSessionEntityType(
        projectId,
        sessionId,
        entityTypeDisplayName
      ) {
        // Imports the Dialogflow library
        const dialogflow = require('dialogflow');

        // Instantiates clients
        const sessionEntityTypesClient = new dialogflow.SessionEntityTypesClient();

        // The path to identify the sessionEntityType to be deleted.
        const sessionEntityTypePath = sessionEntityTypesClient.sessionEntityTypePath(
          projectId,
          sessionId,
          entityTypeDisplayName
        );

        const request = {
          name: sessionEntityTypePath,
        };

        // Send the request for retrieving the sessionEntityType.
        const result = await sessionEntityTypesClient.deleteSessionEntityType(
          request
        );
        console.log(`Session entity type ${entityTypeDisplayName} deleted`);
        return result;
      }

      // /////////////////////////////////////////////////////////////////////////////
      // Command line interface.
      // /////////////////////////////////////////////////////////////////////////////
      const cli = require(`yargs`)
        .demand(1)
        .options({
          projectId: {
            alias: 'p',
            default: process.env.GCLOUD_PROJECT || process.env.GOOGLE_CLOUD_PROJECT,
            description:
              'The Project ID to use. Defaults to the value of the ' +
              'GCLOUD_PROJECT or GOOGLE_CLOUD_PROJECT environment variables.',
            requiresArg: true,
            type: 'string',
          },
        })
        .demandOption(
          'projectId',
          "Please provide your Dialogflow agent's project ID with the -p flag or through the GOOGLE_CLOUD_PROJECT env var"
        )
        .boolean('force')
        .alias('force', ['f'])
        .describe('force', 'force operation without a prompt')
        .command(
          'create-entity-type',
          'Create entity type',
          {
            displayName: {
              alias: 'd',
              string: true,
              demandOption: true,
              requiresArg: true,
              description: 'Display Name',
            },
            kind: {
              alias: 'k',
              demandOption: true,
              requiresArg: true,
              description: 'The kind of entity. KIND_MAP or KIND_LIST.',
            },
          },
          opts => createEntityType(opts.projectId, opts.displayName, opts.kind)
        )
        .command('list-entity-types', 'List entity types', {}, opts =>
          listEntityTypes(opts.projectId)
        )
        .command(
          'delete-entity-type',
          'Delete entity type',
          {
            entityTypeId: {
              alias: 'e',
              string: true,
              demandOption: true,
              requiresArg: true,
              description: 'Session Id',
            },
          },
          opts => deleteEntityType(opts.projectId, opts.entityTypeId)
        )
        .command(
          'create-entity',
          'Create Entity',
          {
            entityTypeId: {
              alias: 'e',
              string: true,
              demandOption: true,
              requiresArg: true,
              description: 'Entity Type Id',
            },
            entityValue: {
              alias: 'v',
              demandOption: true,
              requiresArg: true,
              description: 'Entity Value',
            },
            synonyms: {
              alias: 's',
              array: true,
              demandOption: true,
              requiresArg: true,
              description: 'Synonyms',
            },
          },
          opts =>
            createEntity(
              opts.projectId,
              opts.entityTypeId,
              opts.entityValue,
              opts.synonyms
            )
        )
        .command(
          'list-entities',
          'List entities',
          {
            entityTypeId: {
              alias: 'e',
              string: true,
              demandOption: true,
              requiresArg: true,
              description: 'Entity Type Id',
            },
          },
          opts => listEntities(opts.projectId, opts.entityTypeId)
        )
        .command(
          'delete-entity',
          'Delete entity',
          {
            entityTypeId: {
              alias: 'e',
              string: true,
              demandOption: true,
              requiresArg: true,
              description: 'Entity Type Id',
            },
            entityValue: {
              alias: 'v',
              string: true,
              demandOption: true,
              requiresArg: true,
              description: 'Entity Value',
            },
          },
          opts => deleteEntity(opts.projectId, opts.entityTypeId, opts.entityValue)
        )
        .command(
          'create-context',
          'Create Context',
          {
            sessionId: {
              alias: 's',
              string: true,
              demandOption: true,
              requiresArg: true,
              description: 'Session Id',
            },
            contextId: {
              alias: 'c',
              string: true,
              demandOption: true,
              requiresArg: true,
              description: 'Context Id',
            },
            lifespanCount: {
              alias: 'l',
              demandOption: true,
              requiresArg: true,
              description: 'Lifespan Count',
            },
          },
          opts =>
            createContext(
              opts.projectId,
              opts.sessionId,
              opts.contextId,
              opts.lifespanCount
            )
        )
        .command(
          'list-contexts',
          'List Intents',
          {
            sessionId: {
              alias: 's',
              string: true,
              demandOption: true,
              requiresArg: true,
              description: 'Session Id',
            },
          },
          opts => listContexts(opts.projectId, opts.sessionId)
        )
        .command(
          'delete-context',
          'Delete Context',
          {
            sessionId: {
              alias: 's',
              string: true,
              demandOption: true,
              requiresArg: true,
              description: 'Session Id',
            },
            contextId: {
              alias: 'c',
              string: true,
              demandOption: true,
              requiresArg: true,
              description: 'Context Id',
            },
          },
          opts => deleteContext(opts.projectId, opts.sessionId, opts.contextId)
        )
        .command(
          'create-intent',
          'Create Intent',
          {
            displayName: {
              alias: 'd',
              string: true,
              demandOption: true,
              requiresArg: true,
              description: 'Display Name',
            },
            trainingPhrasesParts: {
              alias: 't',
              array: true,
              string: true,
              demandOption: true,
              requiresArg: true,
              description: 'Training Phrases',
            },
            messageTexts: {
              alias: 'm',
              array: true,
              string: true,
              demandOption: true,
              requiresArg: true,
              description: 'Message Texts',
            },
          },
          opts =>
            createIntent(
              opts.projectId,
              opts.displayName,
              opts.trainingPhrasesParts,
              opts.messageTexts
            )
        )
        .command('list-intents', 'List Intent', {}, opts =>
          listIntents(opts.projectId)
        )
        .command(
          'delete-intent',
          'Delete Intent',
          {
            intentId: {
              alias: 'i',
              string: true,
              demandOption: true,
              requiresArg: true,
              description: 'Intent Id',
            },
          },
          opts => deleteIntent(opts.projectId, opts.intentId)
        )
        .command(
          'create-session-entity-type',
          'Create entity type',
          {
            sessionId: {
              alias: 's',
              string: true,
              demandOption: true,
              requiresArg: true,
              description: 'Display Name',
            },
            entityValues: {
              alias: 'e',
              array: true,
              demandOption: true,
              requiresArg: true,
              description: 'The kind of entity. KIND_MAP or KIND_LIST.',
            },
            entityTypeDisplayName: {
              alias: 'd',
              string: true,
              demandOption: true,
              requiresArg: true,
              description: 'Display Name',
            },
            entityOverrideMode: {
              alias: 'o',
              string: true,
              demandOption: true,
              requiresArg: true,
              description: 'Display Name',
            },
          },
          opts =>
            createSessionEntityType(
              opts.projectId,
              opts.sessionId,
              opts.entityValues,
              opts.entityTypeDisplayName,
              opts.entityOverrideMode
            )
        )
        .command(
          'list-session-entity-types',
          'List entity types',
          {
            sessionId: {
              alias: 's',
              string: true,
              demandOption: true,
              requiresArg: true,
              description: 'Display Name',
            },
          },
          opts => listSessionEntityTypes(opts.projectId, opts.sessionId)
        )
        .command(
          'delete-session-entity-type',
          'Delete entity type',
          {
            sessionId: {
              alias: 's',
              string: true,
              demandOption: true,
              requiresArg: true,
              description: 'Display Name',
            },
            entityTypeDisplayName: {
              alias: 'd',
              string: true,
              demandOption: true,
              requiresArg: true,
              description: 'Display Name',
            },
          },
          opts =>
            deleteSessionEntityType(
              opts.projectId,
              opts.sessionId,
              opts.entityTypeDisplayName
            )
        )
        .example(`node $0 setup-agent`)
        .example(`node $0 show-agent`)
        .example(`node $0 clear-agent`)
        .example(`node $0 update-entity-type "my-entity-type-id"`)
        .example(`node $0 update-intent "my-intent-id"`)
        .example(`node $0 setup-session "my-session-id"`)
        .example(`node $0 show-session "my-session-id"`)
        .example(`node $0 clear-session "my-session-id"`)
        .example(`node $0 update-context "my-session-id" "my-context-id"`)
        .example(
          `node $0 update-session-entity-type "my-session-id" ` +
            `"my-entity-type-name"`
        )
        .wrap(120)
        .recommendCommands()
        .epilogue(
          `For more information, see https://cloud.google.com/dialogflow-enterprise/docs`
        )
        .help()
        .strict();

      if (module === require.main) {
        cli.parse(process.argv.slice(2))};
        
        
        
  다. 마지막으로 terminal 창에서 명령어를 통해 원하는 작업을 수행할 수 있다. 
  
    
    node index.js -p "프로젝트 이름" create-intent -d "새롭게 만들 intent 이름" -t "새롭게 학습 시킬 문장" -m "새롭게 학습시킬 responde"
    
    
    
    
  
  
      
      
  
