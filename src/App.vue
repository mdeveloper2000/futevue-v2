<script setup>
  
    import { onMounted, ref } from 'vue'
    import jogos from './assets/jogos'
    import teams from './assets/teams'    
  
    const rodada = ref(1)
    const games = ref(jogos)
    const times = ref(teams)    
  
    onMounted(() => {    
        times.value.forEach((time) => {
            games.value.forEach((game) => {
                if(game.done) {                    
                    if(game.a === time.nome || game.b === time.nome) {
                        if(game.a == time.nome) {
                            if(game.golsa > game.golsb) {
                                time.vitorias++
                                time.pontos+= 3
                            }
                            else if(game.golsa < game.golsb) {
                                time.derrotas++
                            }
                            else {
                                time.empates++
                                time.pontos++
                            }                           
                        }                                                                    
                    }
                    if(game.b === time.nome || game.a === time.nome) {
                        if(game.b === time.nome) {
                            if(game.golsb > game.golsa) {
                                time.vitorias++
                                time.pontos+= 3
                            }
                            else if(game.golsb < game.golsa) {
                                time.derrotas++
                            }
                            else {
                                time.empates++
                                time.pontos++
                            }
                        }                        
                    }
                    if(game.a === time.nome) {
                        time.gm += parseInt(game.golsa)
                        time.gs += parseInt(game.golsb)
                        time.saldo = parseInt(time.gm - time.gs)
                    }
                    else if(game.b === time.nome) {
                        time.gm += parseInt(game.golsb)
                        time.gs += parseInt(game.golsa)
                        time.saldo = parseInt(time.gm - time.gs)
                    }
                }
            })
        })        
        times.value = times.value.sort(function(a, b) {
            return (b.pontos !== a.pontos ? b.pontos - a.pontos : b.saldo - a.saldo)
        })
        times.value = times.value.sort(function(a, b) {
            return (b.pontos !== a.pontos ? b.pontos - a.pontos : b.vitorias - a.vitorias)
        })
  })

  const simular = (evt, id, time) => {
    let jogo = games.value.filter(game => {
        return game.id === id
    })
    if(jogo !== null) {
        if(time === 'a') {
            jogo[0].golsa = evt.target.value
        }
        else if(time === 'b') {
            jogo[0].golsb = evt.target.value
                }                
            }
            if(jogo[0].golsa !== '' && jogo[0].golsb !== '') {
                jogo[0].done = true                
                if(jogo[0].golsa > jogo[0].golsb) {
                    times.value.forEach(team => {
                        if(team.nome === jogo[0].a) {
                            team.vitorias++                            
                            team.pontos+= 3
                            team.gm = parseInt(team.gm) + parseInt(jogo[0].golsa)
                            team.gs = parseInt(team.gs) + parseInt(jogo[0].golsb)
                            team.saldo = parseInt(team.saldo) + parseInt(jogo[0].golsa - jogo[0].golsb)
                        }
                        else if(team.nome === jogo[0].b) {
                            team.derrotas++
                            team.gm = parseInt(team.gm) + parseInt(jogo[0].golsb)
                            team.gs = parseInt(team.gs) + parseInt(jogo[0].golsa)
                            team.saldo = parseInt(team.saldo) + parseInt(jogo[0].golsb - jogo[0].golsa)                            
                        }                                                
                    })      
                }
                else if(jogo[0].golsa < jogo[0].golsb) {                    
                    times.value.forEach(team => {
                        if(team.nome === jogo[0].a) {
                            team.derrotas++
                            team.gm = parseInt(team.gm) + parseInt(jogo[0].golsa)
                            team.gs = parseInt(team.gs) + parseInt(jogo[0].golsb)
                            team.saldo = parseInt(team.saldo) + parseInt(jogo[0].golsa - jogo[0].golsb)
                        }
                        else if(team.nome === jogo[0].b) {
                            team.vitorias++
                            team.pontos+= 3
                            team.gm = parseInt(team.gm) + parseInt(jogo[0].golsb)
                            team.gs = parseInt(team.gs) + parseInt(jogo[0].golsa)
                            team.saldo = parseInt(team.saldo) + parseInt(jogo[0].golsb - jogo[0].golsa)                            
                        }                                                
                    })
                }
                else {
                    times.value.forEach(team => {
                        if(team.nome === jogo[0].a) {
                            team.empates++
                            team.gm = parseInt(team.gm) + parseInt(jogo[0].golsa)
                            team.gs = parseInt(team.gs) + parseInt(jogo[0].golsb)
                            team.saldo = parseInt(team.saldo) + parseInt(jogo[0].golsa - jogo[0].golsb)
                        }
                        else if(team.nome === jogo[0].b) {
                            team.empates++
                            team.gm = parseInt(team.gm) + parseInt(jogo[0].golsb)
                            team.gs = parseInt(team.gs) + parseInt(jogo[0].golsa)
                            team.saldo = parseInt(team.saldo) + parseInt(jogo[0].golsb - jogo[0].golsa)
                        }                                                
                    })
                }
            times.value = times.value.sort(function(a, b) {
                return (b.pontos !== a.pontos ? b.pontos - a.pontos : b.saldo - a.saldo)
            })                
            times.value = times.value.sort(function(a, b) {                    
                return (b.pontos !== a.pontos ? b.pontos - a.pontos : b.vitorias - a.vitorias)
            })                                                
        }
    }

</script>

<template>

    <div class="container w-75 h-100 mb-5 fs-5 text-bg-light opacity-75 border border-light" id="app">
        <h1 class="text-center">
            <i class="fa-solid fa-futbol fa-spin"></i>&nbsp;Fute<i class="fa-brands fa-vuejs text-success mb-3"></i>ue <span class="fs-6">v2</span>
        </h1>
        <ul class="nav nav-tabs" id="myTab" role="tablist">
            <li class="nav-item" role="presentation">
                <button class="nav-link active" id="home-tab" data-bs-toggle="tab" data-bs-target="#home" 
                    type="button" role="tab" aria-controls="home" aria-selected="true">
                    <i class="fa-solid fa-flag fa-fade"></i> Jogos ({{ rodada }}ª rodada)
                </button>
            </li>
            <li class="nav-item" role="presentation">
                <button class="nav-link" id="profile-tab" data-bs-toggle="tab" data-bs-target="#profile" 
                    type="button" role="tab" aria-controls="profile" aria-selected="false">
                    <i class="fa-solid fa-trophy fa-bounce text-warning"></i> Classificação
                </button>
            </li>
        </ul>

        <div class="tab-content">
        <div class="tab-pane active" id="home" role="tabpanel" aria-labelledby="home-tab" tabindex="0">
            <nav aria-label="Page navigation example">
                <ul class="pagination justify-content-center mt-3">
                    <li class="page-item"><a class="page-link" href="#" @click="rodada > 1 ? rodada-- : ''"><i class="fa-solid fa-backward"></i> Anterior </a></li>
                    <li class="page-item"><a class="page-link" href="#" @click="rodada < 38 ? rodada++ : ''">Próxima <i class="fa-solid fa-forward"></i></a></li>                            
                </ul>
            </nav>
            <table class="table table-primary table-striped">
                <tbody>
                    <tr v-for="jogo in games" :key="jogo.id">              
                        <td class="text-start ps-3 fw-bold time" v-if="jogo.rodada === rodada">{{ jogo.a }}</td>
                        <td class="gols text-end fs-5" v-if="jogo.rodada === rodada">
                            <div v-show="!jogo.done"><input type="text" maxlength="2" @keyup.enter="simular($event, jogo.id, 'a')" /></div>
                            <div v-show="jogo.done">{{ jogo.golsa }}</div>
                        </td>
                        <td class="versus text-center" v-if="jogo.rodada === rodada">X</td>
                        <td class="gols text-start fs-5" v-if="jogo.rodada === rodada">
                            <div v-show="!jogo.done"><input type="text" maxlength="2" @keyup.enter="simular($event, jogo.id, 'b')" /></div>
                            <div v-show="jogo.done">{{ jogo.golsb }}</div>
                        </td>
                        <td class="text-end pe-3 fw-bold time" v-if="jogo.rodada === rodada">{{ jogo.b }}</td>
                    </tr>
                </tbody>
            </table>
        </div>
        
        <div class="tab-pane" id="profile" role="tabpanel" aria-labelledby="profile-tab" tabindex="0">
            <table class="table table-primary text-center">
                <thead class="bg-text-dark">
                    <th></th>
                    <th>TIME</th>
                    <th>PONTOS</th>
                    <th>VITÓRIAS</th>
                    <th>EMPATES</th>
                    <th>DERROTAS</th>
                    <th>GM</th>
                    <th>GS</th>
                    <th>SALDO</th>
                </thead>
                <tbody>
                    <tr v-for="time, index in times" :key="time.nome">
                        <td :class="[(index >= 0 && index <= 3)? 'text-bg-success':'', (index >= 4 && index <= 5) ? 'text-bg-primary':'', (index >= 6 && index <= 11)? 'text-bg-info':'', (index >= 11 && index <= 15)? 'text-bg-secondary':'', (index >= 16)? 'text-bg-danger':'']">{{ index + 1 }}</td>
                        <td>{{ time.nome }}</td>
                        <td>{{ time.pontos }}</td>                           
                        <td>{{ time.vitorias }}</td>
                        <td>{{ time.empates }}</td>
                        <td>{{ time.derrotas }}</td>
                        <td>{{ time.gm }}</td>
                        <td>{{ time.gs }}</td>
                        <td>{{ time.saldo }}</td>
                    </tr>
                </tbody>        
            </table>

            <div class="mt-5 mb-5">
                <span class="rounded-circle bg-success">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</span> <span>Libertadores</span>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                <span class="rounded-circle bg-primary">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</span> <span>Pré-libertadores</span>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                <span class="rounded-circle bg-info">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</span> <span>Sulamericana</span>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                <span class="rounded-circle bg-secondary">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</span> <span>Permanência</span>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                <span class="rounded-circle bg-danger">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</span> <span>Rebaixamento</span>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
            </div>
        </div>
    </div>

</div>

</template>

<style scoped>
.container {
    display: flex;
    flex-direction: column;
    height: 100vh;
}
</style>