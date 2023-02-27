<script setup>
import HelloWorld from './components/HelloWorld.vue'
import TheWelcome from './components/TheWelcome.vue'
</script>

<script>
// import 'mapbox-gl/dist/mapbox-gl.css';
import mapboxgl from 'mapbox-gl';

export default {
    data() {
    return {
      firstname: "Jean",
      age: 30,
      annee: 2022,
      buttonLabel: 'Changer en Patrick',
      isPatrick: false,
      buttonVisible: false,
      family: [
        {
          firstname: "Jojo",
          lastname: "Bernard",
          age: "25",
          address: "1 rue de la Paix",
          phone: "06 12 34 56 78",
          gender: "M"
        },
        {
          firstname: "Marie",
          lastname: "Blachère",
          age: "29",
          address: "2 avenue des Fleurs",
          phone: "06 23 45 67 89",
          gender: "F"
        },
        {
          firstname: "Jean",
          lastname: "Bernard",
          age: "20",
          address: "5 rue de la Liberté",
          phone: "06 34 56 78 90",
          gender: "M"
        },
        {
          firstname: "Paul",
          lastname: "Pogba",
          age: 17,
          address: "10 rue de la Victoire",
          phone: "06 45 67 89 01",
          gender: "M"
        },
      ],
      newMember: {
        firstname: '',
        lastname: '',
        age: null,
        address: '',
        phone: '',
        gender: ""
      },

    }
  },
  methods: {
    changeName() {
      if (this.isPatrick) {
        this.firstname = 'Jean'
        this.buttonLabel = 'Changer en Patrick'
        this.isPatrick = false
      } else {
        this.firstname = 'Patrick'
        this.buttonLabel = 'Changer en Jean'
        this.isPatrick = true
      }
    },
    addFamilyMember() {
      this.family.push({
        firstname: "Jojo",
        lastname: "Bernard",
        age: "25",
        address: "fefzeiu",
        phoneNumber: "crefkn",
        gender: "M"
      })
    },
    removeFamilyMember(index) {
      this.family.splice(index, 1)
    },
    addMember() {
      //Verify age
      if (this.family.age < 10) {
        alert('Âge invalide (doit être supérieur à 10 ans) !');
        return;
      }

      //Add member
      const member = {
            firstname: this.newMember.firstname,
            lastname: this.newMember.lastname,
            age: this.newMember.age,
            address: this.newMember.address,
            phone: this.newMember.phone,
            gender : this.newMember.gender
          };
      this.family.push(member);
      
      //Reset
      this.newMember = {
        firstname: '',
        lastname: '',
        age: '',
        address: '',
        phone: '',
        gender: '',
      }
    },

  },
  mounted() {
    mapboxgl.accessToken = 'pk.eyJ1IjoiaGVucmktcHRoIiwiYSI6ImNsZTc3Z2FoNDAyczAzdXBoY3ppbDNxeW8ifQ.uBIZ4Wsw72kLND_S81Eurg';
    const map = new mapboxgl.Map({
    container: 'map', // container ID
    style: 'mapbox://styles/mapbox/dark-v10', // style URL
    center: [0, 0], // starting position [lng, lat]
    zoom: 2, // starting zoom
    });

    var geocoder = new MapboxGeocoder({
      accessToken: mapboxgl.accessToken,
      mapboxgl: mapboxgl
    });

    map.addControl(geocoder);

    // Parcourir chaque personne dans la liste family
    for (var i = 0; i < this.family.length; i++) {
      var person = this.family[i];

      // Géocodage de l'adresse
      geocoder.query(person.address, function (err, data) {
        if (err) throw err;

        // Récupérer les coordonnées de latitude et longitude
        var coords = data.features[0].center;

        // Créer un marqueur pour la personne
        var marker = new mapboxgl.Marker({ color: 'blue' })
          .setLngLat(coords)
          .addTo(map);

        // Ajouter une info-bulle au marqueur avec le nom de la personne
        var popup = new mapboxgl.Popup()
          .setHTML('<h3>' + person.firstname + ' ' + person.lastname + '</h3><p>Age: ' + person.age + '</p>')
          .addTo(map);

        // Ajouter un événement mouseenter pour afficher l'info-bulle au survol
        marker.on('mouseenter', function () {
          popup.addTo(map);
        });

        // Ajouter un événement mouseleave pour masquer l'info-bulle
        marker.on('mouseleave', function () {
          popup.remove();
        });

        marker.setPopup(popup);
      });
    }

    setInterval(() => {
      this.age++;
      this.annee++;
    }, 3000);

    

  },
  computed: {
    sortedFamily() {
      return this.family.sort((a, b) => a.age - b.age);
    }
  },

}
</script>

<template>
  <main>
    <div class="contenu">
      <div id="presentation">
        <p>Bonjour je m'appelle {{ firstname }}</p>
      </div>

      <div id="age">
        <p>En {{ annee }}, {{ firstname }} aura {{ age }} ans</p>
      </div>

      <div id="family">
        <p  v-for="(member, index) in sortedFamily" :key="index">
          Nom : {{ member.lastname }} | Prénom : {{ member.firstname }} | Âge : {{ member.age }} | 
          Adresse : {{ member.address }} | Téléphone : {{ member.phone }} | Sexe : {{ member.gender }}
          <button @click="removeFamilyMember(index)">Supprimer</button>
        </p>
      </div>
      

      <div>
        <form @submit.prevent="addMember">
          <label for="lastname">Nom :</label>
          <input type="text" id="lastname" v-model="newMember.lastname" required>
          <br>
          <label for="firstname">Prénom :</label>
          <input type="text" id="firstname" v-model="newMember.firstname" required>
          <br>
          <label for="age">Âge :</label>
          <input type="number" id="age" v-model.number="newMember.age" required min="10">
          <br>
          <label for="gender">Genre :</label>
          <select id="gender" v-model="newMember.gender" required>
            <option value="">Sélectionnez un genre</option>
            <option value="M">Homme</option>
            <option value="F">Femme</option>
          </select>
          <br>
          <mapbox-address-autofill access-token="pk.eyJ1IjoiaGVucmktcHRoIiwiYSI6ImNsZTc3Z2FoNDAyczAzdXBoY3ppbDNxeW8ifQ.uBIZ4Wsw72kLND_S81Eurg">
            <label for="address">Adresse :</label>
            <input id="address" v-model="newMember.address">
          </mapbox-address-autofill>
          
          <br>
          <label for="phone">Numéro de téléphone :</label>
          <input type="tel" id="phone" v-model="newMember.phone" required pattern="[0-9]{10}">
          <br>
          <button type="submit">Ajouter</button>
        </form>
      </div>

      <button @click="changeName" id="crazy-button">
        {{ buttonLabel }}
      </button>

      <button v-if="buttonVisible">Hop</button>

      <button id="add-family-member" @click="addFamilyMember">
        Ajouter un membre de la famille
      </button>

      <div id="map"></div>

    </div>


  </main>
</template>

<style scoped>
.contenu{
  background-color: black;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 100vh;
}

p{
  color:white
}

#map{
  width:700px;
  height: 300px;
}
</style>
