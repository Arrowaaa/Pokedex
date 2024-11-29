# POKÉDEX
> *Projeto feito para replicar um pokedex do jogo pokémon, sendo ela usando a linguagem C# e a outra PHP, onde temos como objetivo consultar uma API web que nos retornas as informações de pokemon, como sua imagem, nome e seu número de registro e uma barra de pesquisa para busca pelo pokémon sendo seu nome ou número e 2 botões (Anterior/Próximo).*

> ![GravaodeTela2024-11-29123925-ezgif com-video-to-gif-converter](https://github.com/user-attachments/assets/fae0aad0-1ede-4bc6-9b23-bf2367df08fe)



## Objetivo

**O projeto foi desenvolvido com a turma de TI21, onde o objetivo era os seguintes:**

- [x] **Consultar API**
  - [x] *Retorna a imagem do pokemon*
   - [x] *Retorna as informação do pokemon como por exemplo seu nome*
   - [x] *Retorna as informação do pokemon como por exemplo seu número de registro*
    
- [ ] *Replicar forma fiel de uma pokédex*
- [ ] *Retorna e exibir mais informações do pokémons como ***Base de Status*** HP/ATK/DEF/SPD/EXP*


### Sub Subtitulo

` Código `

<details>
  
  <summary>Codigo da pokedex</summary>
  
        private async void btnConsultaPokemon_Click(object sender, EventArgs e)
        {
            //Você pode digitar o nome do pokemon desejado,e o comando TRIM.. 
            //remove todos os caracteres de espaço em branco antes ou depois do nome digitado.
            string pokemonName = txtNomePokemon.Text.Trim();
            //Obtém o texto digitado no campo, e remove quaisquer espaços em branco extras ao redor.
            //Condição
            
            if (!string.IsNullOrEmpty(pokemonName))
            { //Faz uma Verificação se o nome do Pokémon digitado não está vazio ou nulo.
                //Try tenta executar..
            
                try //Ele permite que você escreva código que pode gerar exceções
                    // neste caso é usado para lidar com exceções que podem ocorrer ao consultar a API do PokeAPI
                {
                    //string apiUrl = $"{ApiBaseUrl} faz a verificação do nome com o site
                    // ToLower converte as letras em minusculas
                    //Constrói a URL completa para consultar informações do Pokémon na API, convertendo o nome do Pokémon para minúsculas.
                    string apiUrl = $"{ApiBaseUrl}{pokemonName.ToLower()}/";
                    // $ Permite que você insira expressões C# diretamente em uma string formatada // $ Permite você junta string com variaveis
                    //sem a necessidade de concatenar strings manualmente.

                    PokemonData pokemonData = await GetPokemonData(apiUrl);
                    
                    //await é usado para esperar a conclusão de uma operação assíncrona sem bloquear o thread atual.
                    //Espera para obter os dados do Pokémon da API de forma assíncrona.
                    
                    if (pokemonData != null)
                    {
                        DisplayPokemonInfo(pokemonData); //para exibir as informações do Pokémon no formulário.
                    }
                    else
                    {
                        //Caixa de mensagem exibe a mensagem se o Pokémon não for encontrado
                        MessageBox.Show("Pokémon não encontrado.");
                    }
                }
                catch (Exception ex) // captura essas exceções e executa um código alternativo 
                                     //Representa erros que ocorrem durante a execução do aplicativo 
                {
                    MessageBox.Show($"Erro ao consultar a API: {ex.Message}"); //Exibe uma caixa de mensagem se ocorrer um erro ao consultar a API.
                }
            }
            else
            {
                MessageBox.Show("Digite o nome de um Pokémon.");
            }
        }

        private void DisplayPokemonInfo(PokemonData pokemonData)
        {
            // Exibir informações do Pokémon
            
            lbID.Text = $"ID: {pokemonData.id}";
            lbName.Text = $"Nome: {pokemonData.name}";
            lbTipo.Text = $"Tipo: {string.Join(", ", pokemonData.types.Select(t => t.type.name))}";
            //Esta parte do código (", ") está unindo os elementos dessa lista de nomes de tipos em uma única string, separando-os por vírgula e espaço
            //Resumindo, essa linha de código configura o texto do Label lbTipo para exibir os nomes dos tipos de Pokémon separados por vírgula e espaço, após o texto "Tipo:".
            //Se um Pokémon tiver mais de um tipo, eles serão exibidos separados por vírgula e espaço.
            lbPeso.Text = $"Peso: {pokemonData.weight} kg";
            lbAltura.Text = $"Altura: {pokemonData.height} m";

            // Exibir imagem do Pokémon
            DisplayPokemonImage(pokemonData.sprites.front_default);
        }
        // Async É usada para fazer uma solicitação HTTP
        // para a API do PokeAPI para obter informações sobre um Pokémon.Isso permite que a aplicação
        // Windows Forms continue respondendo a eventos do usuário enquanto aguarda a resposta da solicitação HTTP,
        // evitando que ela pareça congelada ou não responsiva.
        
        private async Task<PokemonData> GetPokemonData(string apiUrl) 
        
        { //Este método faz uma solicitação assíncrona à API do PokeAPI para obter os dados do Pokémon.
        //método chamado GetPokemonData que recebe uma string apiUrl como parâmetro e retorna uma tarefa (Task) 
        //que produzirá um objeto do tipo PokemonData quando concluída 
        //Indica o tipo específico de objeto que será retornado pela tarefa.
        
            using (HttpClient client = new HttpClient())
            { //Uma biblioteca que garante que o objeto HttpClient seja liberado corretamente após o uso.

                //passando seu parâmetro.. REQUISIÇÃO 
                HttpResponseMessage response = await client.GetAsync(apiUrl); 
                //envia uma solicitação HTTP GET assíncrona para a URL especificada em apiUrl usando um objeto HttpClient, 
                //aguarda a conclusão da solicitação e, em seguida, armazena a resposta HTTP resultante no objeto response.
                
                if (response.IsSuccessStatusCode)
                {
                    string jsonResponse = await response.Content.ReadAsStringAsync(); //Lê o conteúdo da resposta da API como uma string JSON.//JSON ESTRUTURA OU LISTA DE DADOS / INFORMAÇÕES
                    //instale atravez do NuGet Newtonsoft.Json
                    return Newtonsoft.Json.JsonConvert.DeserializeObject<PokemonData>(jsonResponse);
                    //Converte a string JSON em um objeto PokemonData usando a biblioteca 
                }
            }

            return null;
        }

        private void DisplayPokemonImage(string imageUrl) //Exibe a imagem do Pokémon no formulário.
        { //Todos private ou public são uma função // void ele é vazio, depois retorna as informações dentro dos parenteses.


            if (!string.IsNullOrEmpty(imageUrl)) //IsNUllOrEmpty  se não for nulo ou vazio
            {//Define e determina a largura e a altura da área do controle onde a imagem será exibida.
                pictureBox1.Size = new Size(275, 260);
                pictureBox1.SizeMode = PictureBoxSizeMode.StretchImage; //Significa que a imagem será esticada ou reduzida para se ajustar à área do controle (Stretch reduzir)
                pictureBox1.Load(imageUrl); //exiba a imagem obtida da URL. // formato da imagem
            }
        }

        //Classes
        public class PokemonData //Define uma classe para representar os dados de um Pokémon, incluindo seu nome, ID, tipos, peso, altura e sprites.
        {
            public string name { get; set; } //Get permite obter o valor da propriedade //um método getter retorna seu valor, enquanto um método setter o define ou atualiza.
            public int id { get; set; } //Set permite definir o valor da propriedade // "set" significa "colocar, estabelecer" (um valor ao campo); "get" significa "obter" (o valor do campo).
            public List<TypeData> types { get; set; } //é uma estrutura de dados que armazena uma coleção de elementos de um tipo específico. 
            public float weight { get; set; } //Peso
            public float height { get; set; } //Altura
            public Sprites sprites { get; set; }  //nome,id,peso,altura são objetos
        }

        public class TypeData
        { //Define a classe para representar os tipos de um Pokémon.
            public Type type { get; set; }
        }

        public class Type
        { //Ima classe para representar um tipo específico de um Pokémon.
            public string name { get; set; }
        }

        //Classe / biblioteca
        public class Sprites
        { //Define uma classe para representar os sprites (imagens) de um Pokémon.
            public string front_default { get; set; }
        }
    }



