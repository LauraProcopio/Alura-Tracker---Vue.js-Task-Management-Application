<template>
    <div class="box formulario">
        <div class="columns">
            <div class="column is-5" role="form" aria-label="Formulario para a criação de uma nova tarefa">
                <input type="text" class="input" placeholder="Qual tarefa voce deseja iniciar?" v-model="descricao">
            </div>
            <div class="column is-3">
                <div class="select">
                    <select v-model="idProjeto">
                        <option value="">Selecione o projeto</option>
                        <option :value="projeto.id" v-for="projeto in projetos" :key="projeto.id">
                            {{ projeto.nome }}
                        </option>
                    </select>
                </div>
            </div>
            <div class="column">
                <Temporizador @aoTemporizadorFinalizado="finalizarTarefa" />
            </div>
        </div>
    </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue'
import Temporizador from './Temporizador.vue';
import {useStore} from 'vuex';
import { key } from '@/store';
import { computed } from 'vue';
import { NOTIFICAR } from '@/store/tipo-mutacoes';
import { TipoNotificacao } from '@/interfaces/INotificacao';

export default defineComponent({
    name: 'Formulario',
    emits:['aoSalvarTarefa'],
    components: {
        Temporizador
    },
    data (){
        return{
            descricao: '',
            idProjeto: ''
        }
    },
    methods: {
        finalizarTarefa (tempoDecorrido: number) : void{
            const projeto = this.projetos.find((p) => p.id == this.idProjeto);
           if(!projeto){
            this.store.commit(NOTIFICAR,{
                titulo:'Ops!',
                texto: "Selecione um projeto antes de finalizar a tarefa",
                tipo: TipoNotificacao.FALHA,
            });
            return;
           }
           this.$emit('aoSalvarTarefa', {
            duracaoEmSegundos: tempoDecorrido,
            descricao: this.descricao,
            projeto: projeto
           })
           this.descricao = ''
        }
    },
    setup(){
        const store = useStore(key); // Acessando o store do Vuex
        const projetos = computed(() => store.state.projetos); // Acessando os projetos do store

        return {
            store,
            projetos // Passando os projetos para o template
        }
    }
});
</script>

<style>
.formulario{
    color: var(--texto-primario);
    background-color: var(--bg-primario);

}
</style>