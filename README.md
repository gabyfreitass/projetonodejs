const express = require('express');
const app = express();
const PORT = 3000;

app.use(express.json());

let alunos = [];

// Função para calcular a média e a situação
const calcularMediaESituacao = (nota1, nota2) => {
    const media = (nota1 + nota2) / 2;
    let situacao;
    if (media >= 7) {
        situacao = 'aprovado';
    } else if (media >= 4) {
        situacao = 'recuperação';
    } else {
        situacao = 'reprovado';
    }
    return { media, situacao };
};

// Cadastrar Aluno
app.post('/alunos', (req, res) => {
    const { nome, nota1, nota2 } = req.body;

    if (nota1 < 0 || nota1 > 10 || nota2 < 0 || nota2 > 10) {
        return res.status(400).json({ mensagem: 'Informe uma nota válida' });
    }

    const { media, situacao } = calcularMediaESituacao(nota1, nota2);
    const aluno = { id: alunos.length + 1, nome, nota1, nota2, media, situacao };
    alunos.push(aluno);
    res.status(201).json(aluno);
});

// Listar Alunos
app.get('/alunos', (req, res) => {
    res.json(alunos);
});

// Alterar dados de um Aluno
app.put('/alunos/:id', (req, res) => {
    const { id } = req.params;
    const { nome, nota1, nota2 } = req.body;
    const aluno = alunos.find(a => a.id == id);

    if (!aluno) {
        return res.status(404).json({ mensagem: 'Aluno não encontrado' });
    }

    if (nota1 < 0 || nota1 > 10 || nota2 < 0 || nota2 > 10) {
        return res.status(400).json({ mensagem: 'Informe uma nota válida' });
    }

    const { media, situacao } = calcularMediaESituacao(nota1, nota2);
    aluno.nome = nome;
    aluno.nota1 = nota1;
    aluno.nota2 = nota2;
    aluno.media = media;
    aluno.situacao = situacao;

    res.json(aluno);
});

// Deletar um Aluno
app.delete('/alunos/:id', (req, res) => {
    const { id } = req.params;
    const index = alunos.findIndex(a => a.id == id);

    if (index === -1) {
        return res.status(404).json({ mensagem: 'Aluno não encontrado' });
    }

    alunos.splice(index, 1);
    res.status(204).send();
});

// Retornar o nome e média de um aluno pelo id
app.get('/alunos/:id/media', (req, res) => {
    const { id } = req.params;
    const aluno = alunos.find(a => a.id == id);

    if (!aluno) {
        return res.status(404).json({ mensagem: 'Aluno não encontrado' });
    }

    res.json({ nome: aluno.nome, media: aluno.media });
});

// Iniciar o servidor
app.listen(PORT, () => {
    console.log(`Servidor rodando na porta ${PORT}`);
});
