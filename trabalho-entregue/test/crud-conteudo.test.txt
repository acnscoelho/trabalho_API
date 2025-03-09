const request = require('supertest');
const { faker } = require('@faker-js/faker');

const{
    URLS,
    HEADERS
} = require('../../suporte/configEnv')

describe('Descrição da suíte de testes crud (post, get, put, delete CONTEUDOS)', ()=>{

    let recebeId;

    beforeAll(async()=>{
            const payloadConteudos ={
                "titulo": faker.person.jobTitle(),
                "descricao": faker.person.jobDescriptor(),
                "tipoConteudo": "Teste de Unidade",
                "conteudo": faker.person.jobArea()
            }
    
            const responsePost = await request(URLS.ROTA_ENDPOINT)
            .post('/conteudos')
            .send(payloadConteudos)
    
            recebeId = responsePost.body.id;
            expect(responsePost.status).toBe(201);
            expect(recebeId).toBeDefined();
    
            console.log('Conteúdo criado com sucesso:', responsePost.body);        

    });

    it('Test 02: deve consultar o registro cadastrado anteriormente, e validar resultado e statusCode', async()=>{
        const responseGet = await request(URLS.ROTA_ENDPOINT)
        .get(`/conteudos/${recebeId}`)

        expect(responseGet.status).toBe(200);
        expect(responseGet.body.id).toBe(recebeId);

        console.log('Conteúdo encontrado: ', responseGet.body);


    })

    it('Test 03: deve alterar o conteudo cadastrado anteriormente, e validar que os dados realmente foram alterados e validar statusCode', async()=>{
        const novoConteudo = {
            "titulo": faker.person.jobTitle(),
            "descricao": faker.person.jobDescriptor(),
            "tipoConteudo": "Teste de Performance",
            "conteudo": faker.person.jobArea()
        }

        const responsePut = await request(URLS.ROTA_ENDPOINT)
            .put(`/conteudos/${recebeId}`)
            .send(novoConteudo)

        expect(responsePut.status).toBe(201);

        expect(responsePut.body.titulo).toBe(novoConteudo.titulo);
        expect(responsePut.body.descricao).toBe(novoConteudo.descricao);

        console.log('Conteúdo atualizado com sucesso: ', responsePut.body);
        const responseGet = await request(URLS.ROTA_ENDPOINT)
            .get(`/conteudos/${recebeId}`)

        expect(responseGet.status).toBe(200);
        expect(responseGet.body.id).toBe(recebeId);
        expect(responseGet.body.titulo).toBe(novoConteudo.titulo);
        expect(responseGet.body.descricao).toBe(novoConteudo.descricao)

    })

    it('Test 04: deve remover o registro cadastrado, e validar a consulta do registro para garantir sua remoção.', async()=>{
        const responseDelete = await request(URLS.ROTA_ENDPOINT)
        .delete(`/conteudos/${recebeId}`)

        expect(responseDelete.status).toBe(200);
        console.log('Resposta do delete:', responseDelete.body)

        const responseGet = await request(URLS.ROTA_ENDPOINT)
        .get(`/conteudos/${recebeId}`)

        expect(responseGet.status).toBe(404);
        expect(responseGet.body).toEqual({ error: `O conteúdo com o ID: ${recebeId} não foi encontrado.` });
        console.log(responseGet.body);    

    })

})
