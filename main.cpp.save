#include <allegro5/allegro.h>
#include <allegro5/allegro_primitives.h>
#include <iostream>
#include <allegro5/allegro_font.h>
#include <allegro5/allegro_ttf.h>
#include <allegro5/allegro_native_dialog.h>
#include <allegro5/allegro_audio.h>
#include <allegro5/allegro_acodec.h>

/* **********************************
TRABALHO BIMESTRAL - LINGUAGEM C
PROFESSOR LUIZ FERNANDO CORCINI
ALUNOS: ANA KARINA WENESKI SOCODOLSKI
        EDERSOM DENIR GIACOMELLI
        JOSÉ CARLOS NOWAZESKI FILHO
********************************** */
ALLEGRO_SAMPLE *Trilha_Sonora = NULL;
ALLEGRO_SAMPLE_INSTANCE * inst_Trilha_Sonora = NULL;
ALLEGRO_SAMPLE *Laser = NULL;
ALLEGRO_SAMPLE_INSTANCE * inst_Laser = NULL;
ALLEGRO_SAMPLE *Explosao = NULL;
ALLEGRO_SAMPLE_INSTANCE * inst_Explosao = NULL;
ALLEGRO_SAMPLE *Aplauso = NULL;
ALLEGRO_SAMPLE_INSTANCE * inst_Aplauso = NULL;

using namespace std;

class jogador
{
    public:
        int PosX;
        int PosY;
        int Balas;
        int Pontos;
        bool recarregou;
    jogador()
    {
        cout << "jogador instanciado" << endl;
        //posição inicial
        this->PosX = 0;
        this->PosY = 0;
        //total de balas
        this->Balas = 10;
        //total de pontos
        this-> Pontos = 0;
        this-> recarregou = false;
        cout <<"caracteristicas definidas do jogador"<< endl;
    }
    void desenha_Jogador()
    {
        al_draw_circle(this-> PosX, this->PosY, 20, al_map_rgb(255,255,0), 1);
        al_draw_line(this-> PosX-5, this->PosY, this->PosX-20, this->PosY, al_map_rgb(255,255,0), 1);
        al_draw_line(this-> PosX, this->PosY-5, this->PosX, this->PosY-20, al_map_rgb(255,255,0), 1);
        al_draw_line(this-> PosX+5, this->PosY, this->PosX+20, this->PosY, al_map_rgb(255,255,0), 1);
        al_draw_line(this-> PosX, this->PosY+5, this->PosX, this->PosY+20, al_map_rgb(255,255,0), 1);
    }
    void atualiza_Jogador(int x, int y)
    {
        this->PosX = x-15;
        this->PosY = y-15;
    }
    void atuliza_Municao ()
    {
        this->Balas = this->Balas-1;
        cout << "Balas: " << this->Balas <<endl;
    }
    void atuliza_Placar (bool Acertou)
    {
        if (Acertou)
        {
            this->Pontos = this->Pontos+1;
            cout << "Pontos: " << this->Pontos <<endl;

            if (this->Pontos == 8 && !this->recarregou) {
                al_play_sample_instance(inst_Aplauso);
            } else if (this->Pontos == 30) {
                al_play_sample_instance(inst_Aplauso);
            }
        }
    }
    bool verifica_Municao()
    {
        bool retorno = true;
        if (this-> Balas ==0)
            retorno = false;
        return retorno;
    }
    void visualiza_Placar(ALLEGRO_FONT *Arial)
    {
        al_draw_textf(Arial, al_map_rgb(255,255,255), 20, 20, ALLEGRO_ALIGN_LEFT, "BALAS: %d // PONTOS: %d", this->Balas, this->Pontos);
    }
};

class inimigo
{
    public:
        //atributos
        float cX;
        float cY;
        int Raio;
        float Veloc;
        int DirX;
        int DirY;
        bool Ativo;
        ALLEGRO_COLOR Cor;
        //metodos
        inimigo()  //construtor
        {
            cout << "inimigo instanciado" << endl;
            this->cX = rand()% 750 + 40; //gera números entre 40 e 750
            this->cY = rand()% 550 + 40; //gera números entre 40 e 550
            this->Raio = rand()% 40 + 15; //gera números entre 15 e 40
            this->Veloc = (rand()%10+1)*0.01;
            this->DirX = rand() %6-3;
            this->DirY = rand() %6-3;
            this->Ativo = true;
            int r = rand() %255+10;
            int g = rand() %255+50;
            int b = rand() %255+70;
            this->Cor = al_map_rgb(r,g,b);
            cout << "caracteristicas definidas" << endl;
        }
        void desenha_Inimigo()
        {
            if (this->Ativo)
                al_draw_filled_circle(this->cX, this->cY, this->Raio, this->Cor);
            else
            {
                int n = rand()%1000;
                if (n==0)
                    this->Ativo = true;
            }
        }
        void atualiza_inimigo()
        {
            this->cX = this->cX + this-> DirX*this->Veloc;
            this->cY = this->cY + this-> DirY*this->Veloc;
        }
        void verifica_Limites (int LARGURA, int ALTURA)
        {
            //verifica lado direito
            if (this->cX + this-> Raio >=LARGURA)
                this->DirX = this->DirX*(-1);
            //verifica o lado esquerdo
            if (this->cX - this-> Raio <=0)
                this->DirX = this->DirX*(-1);
            //verifica o lado superior
            if (this->cY + this-> Raio >=ALTURA)
                this->DirY = this->DirY*(-1);
            //verifica o lado superior
            if (this->cY - this-> Raio <=0)
                this->DirY = this->DirY*(-1);
        }
        bool verifica_Disparo (int x_disparo, int y_disparo)
        {
            bool ACERTOU = false;
            if (x_disparo >= this -> cX - this-> Raio && x_disparo <= cX + this-> Raio)
            {
                if (y_disparo >= this -> cY - this-> Raio && y_disparo <= cY + this-> Raio)
                {
                    al_stop_sample_instance(inst_Explosao);
                    al_play_sample_instance(inst_Explosao);
                    this->Ativo = false;
                    cout << "ACERTOU O ALVO" << endl;
                    ACERTOU = true;
                }
            }
            return ACERTOU;
        }
};

void Objetos_Colisoe_Sons_1()
{
    srand(time(NULL));  // numeros aleatórios a cada geração
    //Variáveis Globais
    int LARGURA = 800;
    int ALTURA = 600;
    bool CONTINUAR = true;
    int PosX_Jogador;
    int PosY_Jogador;
    //Variáveis allegro
    ALLEGRO_DISPLAY *Tela = NULL;
    ALLEGRO_EVENT_QUEUE *Fila_Eventos = NULL;
    ALLEGRO_EVENT ev;
    //incia bibliotecas e addons
    al_init();
    al_init_primitives_addon();
    al_install_mouse();
    al_install_keyboard();
    al_init_font_addon();
    al_init_ttf_addon();
    //audio
    al_init_acodec_addon();
    al_install_audio();
    al_reserve_samples(10);



        //instanciar inimigos
        inimigo INI_01;
        inimigo INI_02;
        inimigo INI_03;

        //instanciar jogador
        jogador PLAYER;

    //variaveis cor
    ALLEGRO_COLOR Cor_Fundo = al_map_rgb(0,0,0);
    ALLEGRO_COLOR Cor_Ret = al_map_rgb(255,255,255);

    //texto
    ALLEGRO_FONT *Arial = al_load_font("arial.ttf", 40, NULL);

    //criação da fila de eventos
    Fila_Eventos = al_create_event_queue();

    //Cria tela
    Tela = al_create_display(LARGURA, ALTURA);

    al_clear_to_color(al_map_rgb(0,0,0));
    al_draw_text(Arial, al_map_rgb(255,255,255), LARGURA/2, ALTURA/2, ALLEGRO_ALIGN_CENTRE, "carregando........");
    al_flip_display();
    //audios
    //trilha sonora
    Trilha_Sonora = al_load_sample("trilha_sonora.ogg"); //carrega a trilha sonora
    inst_Trilha_Sonora = al_create_sample_instance(Trilha_Sonora); // instanciar a trilha sonora
    al_attach_sample_instance_to_mixer(inst_Trilha_Sonora, al_get_default_mixer()); //mixar a trilha sonora
    al_set_sample_instance_playmode(inst_Trilha_Sonora, ALLEGRO_PLAYMODE_LOOP); //define a repetição durante todo o jogo
    al_set_sample_instance_gain(inst_Trilha_Sonora, 0.8); //volume do som

    //laser
    Laser = al_load_sample("laser.wav"); //carrega o laser
    inst_Laser = al_create_sample_instance(Laser); // instanciar o laser
    al_attach_sample_instance_to_mixer(inst_Laser, al_get_default_mixer()); // mixar o laser
    //explosão
    Explosao = al_load_sample("explosao.wav"); //carrega a explosao
    inst_Explosao = al_create_sample_instance(Explosao); // instanciar a explosao
    al_attach_sample_instance_to_mixer(inst_Explosao, al_get_default_mixer()); // mixar o laser
    //aplauso
    Aplauso = al_load_sample("aplausos.wav"); //carrega a explosao
    inst_Aplauso = al_create_sample_instance(Aplauso); // instanciar a explosao
    al_attach_sample_instance_to_mixer(inst_Aplauso, al_get_default_mixer()); // mixar o laser

    //esconde o ponteiro do mouse
    al_hide_mouse_cursor(Tela);

    //defina a fonte dos eventos (teclado e mouse)
    al_register_event_source(Fila_Eventos, al_get_keyboard_event_source());
    al_register_event_source(Fila_Eventos, al_get_mouse_event_source());

    //limpa a tela
    al_clear_to_color(Cor_Fundo);

    //atualiza a tela
    al_flip_display();

    //tocar a trilha sonora
    al_play_sample_instance(inst_Trilha_Sonora);

    //loop principal
while (CONTINUAR)
    {
            //LIMPA TELA, REDESENHA E ATUALIZA
            al_clear_to_color(al_map_rgb(0,0,0));
            //coloca inimigos na tela
            INI_01.desenha_Inimigo();
            INI_02.desenha_Inimigo();
            INI_03.desenha_Inimigo();
            //cria o jogador na tela
            PLAYER.desenha_Jogador();
            //movimento inimigos
            INI_01.atualiza_inimigo();
            INI_02.atualiza_inimigo();
            INI_03.atualiza_inimigo();
            //limites inimigos
            INI_01.verifica_Limites(LARGURA, ALTURA);
            INI_02.verifica_Limites(LARGURA, ALTURA);
            INI_03.verifica_Limites(LARGURA, ALTURA);
            PLAYER.visualiza_Placar(Arial);
            al_flip_display();
            if(!al_is_event_queue_empty(Fila_Eventos))
        {
            //vai aguardar algo acontecer e para inserir na fila
            al_wait_for_event(Fila_Eventos, &ev);
            //tratamento com a tecla esc - se apertada sai da tela
            if (ev.type == ALLEGRO_EVENT_KEY_DOWN)
            {
                 if(ev.keyboard.keycode == ALLEGRO_KEY_ESCAPE)
                    CONTINUAR = false;
                al_clear_to_color(al_map_rgb(0,0,0));
                al_draw_text(Arial, al_map_rgb(255,255,255), LARGURA/2, ALTURA/2, ALLEGRO_ALIGN_CENTRE, "Jogo encerrado");
                al_flip_display();
                al_rest(3);
            }
            //captura o movimento do mouse (seleciona as suas posições x e y)
            else if (ev.type == ALLEGRO_EVENT_MOUSE_AXES)
            {
                PosX_Jogador = ev.mouse.x;
                PosY_Jogador = ev.mouse.y;
                PLAYER.atualiza_Jogador(PosX_Jogador, PosY_Jogador);
            }

            //captura o clique o botão e envia a mensagem no console
            else if (ev.type == ALLEGRO_EVENT_MOUSE_BUTTON_DOWN)
                {
                    if (ev.mouse.button == 1) //botao esquerdo
                    {
                        if (PLAYER.verifica_Municao())
                        {
                        al_stop_sample_instance(inst_Laser);
                        al_play_sample_instance(inst_Laser);
                        PLAYER.atuliza_Municao();
                        bool acertou1 = INI_01.verifica_Disparo(PosX_Jogador, PosY_Jogador);
                            PLAYER.atuliza_Placar(acertou1);
                        bool acertou2 = INI_02.verifica_Disparo(PosX_Jogador, PosY_Jogador);
                            PLAYER.atuliza_Placar(acertou2);
                        bool acertou3 = INI_03.verifica_Disparo(PosX_Jogador, PosY_Jogador);
                            PLAYER.atuliza_Placar(acertou3);
                        cout <<"-----------" << endl;
                        }
                        else
                        {
                            //cout << "ACABOU A MUNICAO" << endl;
                            int resposta = al_show_native_message_box(Tela, "Aviso", "Balas acabaram", "Deseja Recarregar?", NULL, ALLEGRO_MESSAGEBOX_YES_NO);
                            if (resposta == 1) {
                                PLAYER.Balas = 10;
                                PLAYER.recarregou = true;
                            }
                        }
                    }

                }

            }
    }
    //limpa
    al_destroy_display(Tela);
    al_destroy_event_queue(Fila_Eventos);
    al_destroy_font(Arial);
    al_destroy_sample(Trilha_Sonora);
    al_destroy_sample(Laser);
    al_destroy_sample(Explosao);
    al_destroy_sample(Aplauso);

    al_destroy_sample_instance(inst_Trilha_Sonora);
    al_destroy_sample_instance(inst_Laser);
    al_destroy_sample_instance(inst_Explosao);
    al_destroy_sample_instance(inst_Aplauso);
}
int main ()
{
    Objetos_Colisoe_Sons_1(); // Desafios
    //Desafios 05, 06, 07 no documento Pdf separado

    return 0;
}
