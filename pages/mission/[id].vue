<template>
    <div class="mission" v-if="mission" :class="`-color-${store.mainColor}`">
        <NuxtLayout :class="`-main_color-${store.mainColor}`">

            <template #header>
                <h1 class="mission__h1">Mission</h1>
            </template>
    
            <template #aside v-if="mission">
                <div class="mission__aside--stiuation">
                    <h2 class="global-titre_texte">Situation : </h2>
                    <p class="mission__texte--aside">{{jour_last.situation}}</p>
                </div>

                <div class="mission__aside--objectifs">
                    <h2 class="global-titre_texte">Objectifs : </h2>
                    <ul class="mission__texte--aside">
                        <li v-for="ob in jour_last.objectifs">{{ob}}</li>
                    </ul>
                </div>

                <div class="mission__aside--etat">
                    <h2 class="global-titre_texte">état de Spike : </h2>

                    <div class="etat">
                        <div class="niveau">
                            <div class="niveau__bloc"><div class="niveau__variable" :style="{ width: level(jour_last.faimsoif) }"></div></div>
                            <p class="mission__texte--aside niveau__nom">nourriture</p>
                        </div>

                        <div class="niveau">
                            <div class="niveau__bloc"><div class="niveau__variable" :style="{ width: level(jour_last.physique) }"></div></div>
                            <p class="mission__texte--aside niveau__nom">physique</p>
                        </div>

                        <div class="niveau">
                            <div class="niveau__bloc"><div class="niveau__variable" :style="{ width: level(jour_last.mentale) }"></div></div>
                            <p class="mission__texte--aside niveau__nom">santé mentale</p>
                        </div>

                        <div class="tete"><tete3d/></div>
                    </div>
                </div>
            </template>

            <div v-if="mission">
                <h2 class="mission__titre">Mission n°<span class="mission__titre--num">{{ mission.id_mission }}</span> du <span class="mission__titre--date">{{ mission.date }}</span></h2>
                <h2 class="mission__titre">Resultat : <span class="mission__titre--date">{{ mission.resultat }}</span></h2>
    
                <div class="mission__jour" v-for="(etape, index) in mission.partie" :key=index+1>
                    <h2 class="mission__jour--titre global-titre_texte">Jour {{ index+1 }} :</h2>
                    <p class="mission__texte" v-html="etape.texte"></p>
                </div>
    
                <div class="mission__jour--buttons">
                    <myButton type="t_button" :color="`main_color-${store.mainColor}`" link="/historique">Retour Historique</myButton>
                    <myButton type="t_button" :color="`main_color-${store.mainColor}`" link="/desktop">Retour Bureau</myButton>
                </div>
            </div>

        </NuxtLayout>
    </div>
</template>

<script setup>
import {API} from '@/utils/axios'

const store = useGlobalStore()

const route = useRoute();
const mission = ref()
const jour_last = ref()

const currentDate = new Date();

// récupération de la mission
const getMission = async () => {
    const response = await API.get(`/mission/${route.params.id}`)
    mission.value = response.data
    mission.value.id_mission = mission.value.id_mission.toString().padStart(3, '0')
    jour_last.value = mission.value.partie[mission.value.partie.length-1]

    jour_last.value.objectifs = jour_last.value.objectifs.split(',').map(objectif => objectif.trim());
}

// Fonction pour calculer la largeur en fonction de la valeur de faimsoif
const level = (niv) => {
    const width = niv * 10 + '%';
    return width;
};

const mainColor = computed(() => ({
    " -good": jour_last.value.decision === "good",
    " -bad": jour_last.value.decision === "bad",
}));

// chargement de la base de données
onMounted(async() => {
    await getMission()
})

useSeoMeta({
    title: "Acienne Mission - How To Meet Aliens ?",
    description: "Une Acienne Mission du jeu narratif How To Meet Aliens ?",
    ogTitle: "How To Meet Aliens ? Page d'une Acienne Mission",
    ogDescription: "Une Acienne Mission du jeu narratif How To Meet Aliens ?",
    ogImage: "/how_to_meet_aliens_logo.png",
})

// renvoie l'user sur l'index s'il n'est pas connecté
definePageMeta({
    middleware: [
        function (to, from) {},
        'auth',
    ],
});
</script>