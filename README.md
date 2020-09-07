* Setup
    npm i graphql-yoga --save
    //um raper em cima do express que integra funcionalidades
    do graphql e o insominia integrado para manipular as querys


* Criando servidor
    Após instalação, basta importar e instanciar o GraphQLServer de dentro do graphql-yoga 

    const server = new GraphQLServer({
        typeDefs: path.resolve(__dirname, 'schema.graphql')
    });

    O typeDefs irá ler as rotas/schemas e atribui-los ao server, o resolvers irá fazer o papel de controller no contexto do grapql


* Rotas/ typeDefs
    O arquivo schema.graphql será o controle de rotas do graphql

    * type Query -> GET
    * type Mutation -> PUT/ POST/ PATCH/ DELETE
    * type Subscription -> socket.io/ real time


* Tipagem
    * O graphql é fortemente tipado, igual ao typescript, podemos criar um tipo para representar uma rota, igual usar uma interface em typescript.

        por ex:
            type User {
                name: String
                age: Number
            }

            type Query {
                users: [User!]!  // informa que o endpoint/query irá retornar um array de usuários
                                // o ponto de exclamação dentro do array indica que todos os objetos dentro desse array devem ser do tipo User.  
                                // o ponto de exclamação de fora do array, informa que a rota users deve SEMPRE retornar um array, mesmo que vazio 


                user(id: ID!): User // Cria outra rota/query para retornar apenas um usuário, via parametro recebe o ID do usuário que não pode ser nullo.
                                    // Em seguida é informado que o tipo de retorno dessa Query será um User.
            
            }


            type Mutation {                                           // Outro Schema referente ao usuário, do tipo Muatation
                createUser(name: String!, email: String!): User       // createUser é um shcema que recebe params necessários para criar e retornar um User.
            }
                    



    * Quando queremos definir um tipo id, utilizamod o ID, se quisermos utilizar o ! para indicar que aquele campo é obrigatorio, utilizamos o ID!



* Resolvers/ Controllers
        * O Resolver será um objeto que irá resolver os schemas, também irá ter os dois tipos, Query e Mutation, que possuirá duas funções se baseando nos schemas que foram declarados.
        
        const User = require('./User');

        module.exports = {
            Query: {
                users: () => User.find(),
                user: (_, {                              // o primeiro param é o contexto, por isso utilizado o _, sómente uma variavel para receber tudo
                    id
                }) => User.findById(id)
            },

            Mutation: {
                createUser: (_, {
                    name,
                    email
                }) => User.create({
                    name,
                    email
                })
            }
        };



* Running
    * node src/server.js
    acesse http://localhost:4000/

    * Rode as querys no insomina integrado do grapql
        // retornar todos usuarios
        query {
            users {
                id                  // selec das props
                name 
                email
            }
        }

        // criação de usuarios, as mutations podem ter nome tambem
    
        mutation {
            createUser(name:"graphql", email:"graphql@teste.com") {
                id,
                name
            }
        }