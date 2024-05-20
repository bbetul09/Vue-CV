<template>
  <main class="container">
    <EditToggle @edit-mode-toggled="toggleEditMode"/>
    <ExportPdf v-if="!editing"/>

    <div id="resume" class="d-flex" :class="{'edit-off': !editing}">
      <div class="left-col">
        <ResumeSection>
          <img
            v-bind:src="imageUrl"
            class="profile-pic"
            alt="profile picture" />

          <SectionHeadline
            :headline="headlines[0]"
            @headline-edited="updateHeadline($event, 0)"
            :editing="editing"
          />

          <div
            :contenteditable="editing"
            @input="updateProperty($event, 'introText')">
            {{ introText }}
          </div>
        </ResumeSection>

        <ResumeSection>
          <SectionHeadline
            :headline="headlines[1]"
            @headline-edited="updateHeadline($event, 1)"
            :editing="editing"
          />

          <Contact
            :contact="contact"
            @edit="updateNestedProperty"
            :editing="editing"
          />
        </ResumeSection>

        <ResumeSection>
          <SectionHeadline
            :headline="headlines[2]"
            @headline-edited="updateHeadline($event, 2)"
            :editing="editing"
          />

          <ul>
            <li
              v-for="(skill, index) in skills"
              :key="index"
              :contenteditable="editing"
              @input="updateNestedProperty($event, 'skills', index)">
              {{ skill }}
            </li>
          </ul>
          <EditButtons
            @add-click="skills.push('new entry')"
            @remove-click="skills.pop()"
            :show-remove-btn="skills.length > 0" />
        </ResumeSection>

        <ResumeSection>
          <SectionHeadline
            :headline="headlines[3]"
            @headline-edited="updateHeadline($event, 3)"
            :editing="editing"
          />
          <ul>
            <li
              v-for="(highlight, index) in highlights"
              :key="index"
              :contenteditable="editing"
              @input="updateNestedProperty($event, 'highlights', index)">
              {{ highlight }}
            </li>
          </ul>
          <EditButtons
            @add-click="highlights.push('new entry')"
            @remove-click="highlights.pop()"
            :show-remove-btn="highlights.length > 0" />
        </ResumeSection>
      </div>

      <div class="right-col">
        <div
          class="personal-name"
          :contenteditable="editing"
          @input="updateProperty($event, 'name')">
          {{ name }}
        </div>

        <div
          class="personal-title"
          :contenteditable="editing"
          @input="updateProperty($event, 'title')">
          {{ title }}
        </div>

        <ResumeSection>
          <div class="d-flex">
            <SectionHeadline
              :headline="headlines[4]"
              @headline-edited="updateHeadline($event, 4)"
              :editing="editing"
            />
            <EditButtons
              :show-remove-btn="false"
              text-add="Add experience"
              @add-click="addExperience"
            />
          </div>

          <div
            v-for="(item, index) in experience"
            :key="index"
            class="inner-section">
            <div class="d-flex justify-content-between">
              <div
                :contenteditable="editing"
                @input="updateExperience($event, 'title', index)">
                {{ item.title }}
              </div>
              <EditButtons @remove-click="removeExperience(index)" :show-add-btn="false"/>
            </div>
            <div class="d-flex justify-content-between">
              <div>
                <span
                  :contenteditable="editing"
                  @input="updateExperience($event, 'company', index)">
                  {{ item.company }} </span
                >,
                <span
                  :contenteditable="editing"
                  @input="updateExperience($event, 'location', index)">
                  {{ item.location }}
                </span>
              </div>
              <div
                :contenteditable="editing"
                @input="updateExperience($event, 'date', index)">
                {{ item.date }}
              </div>
            </div>

            <ul>
              <li
                v-for="(desc, innerIndex) in item.description"
                :key="innerIndex"
                :contenteditable="editing"
                @input="updateExperienceDescription($event, index, innerIndex)">
                {{ desc }}
              </li>
            </ul>
            <EditButtons
              @add-click="item.description.push('new entry')"
              @remove-click="item.description.pop()"
              :show-remove-btn="item.description.length > 0" />
          </div>
        </ResumeSection>

        <ResumeSection>
          <div class="d-flex">
            <SectionHeadline
              :headline="headlines[5]"
              @headline-edited="updateHeadline($event, 5)"
              :editing="editing"
            />
            <EditButtons
              :show-remove-btn="false"
              text-add="Add education"
              @add-click="addEducation"
            />
          </div>

          <div
            v-for="(item, index) in education"
            :key="index"
            class="inner-section">

            <div class="d-flex justify-content-between">
              <div
                :contenteditable="editing"
                @input="updateEducation($event, 'title', index)">
                {{ item.title }}
              </div>
              <EditButtons @remove-click="removeEducation(index)" :show-add-btn="false"/>
            </div>

            <div class="d-flex justify-content-between">
              <div>
                <span
                  :contenteditable="editing"
                  @input="updateEducation($event, 'university', index)">
                  {{ item.university }} </span
                >,
                <span
                  :contenteditable="editing"
                  @input="updateEducation($event, 'location', index)">
                  {{ item.location }}
                </span>
              </div>

              <div
                :contenteditable="editing"
                @input="updateEducation($event, 'date', index)">
                {{ item.date }}
              </div>
            </div>

            <ul>
              <li
                v-for="(desc, innerIndex) in item.description"
                :key="innerIndex"
                :contenteditable="editing"
                @input="updateEducationDescription($event, index, innerIndex)">
                {{ desc }}
              </li>
            </ul>
            <EditButtons
              @add-click="item.description.push('new entry')"
              @remove-click="item.description.pop()"
              :show-remove-btn="item.description.length > 0" />
          </div>
        </ResumeSection>
      </div>
    </div>
  </main>
</template>

<script>
import ResumeSection from "./components/ResumeSection.vue";
import SectionHeadline from "./components/SectionHeadline.vue";
import Contact from "./components/Contact.vue";
import EditButtons from "./components/EditButtons.vue";
import EditToggle from "./components/EditToggle.vue";
import ExportPdf from "./components/ExportPdf.vue"

export default {
  components: {
    ResumeSection,
    SectionHeadline,
    Contact,
    EditButtons,
    EditToggle,
    ExportPdf
  },
  data() {
    return {
      name: "Michaela Scarn",
      title: "Senior Data Scientist",
      introText:
        "From data cleaning to data analysis to machine learning, I am passionate about everything data.",
      imageUrl: "/cv-logo.png",
      headlines: [
        "About me",
        "Contact",
        "Skills",
        "Certifications",
        "Experience",
        "Education",
      ],
      contact: {
        phone: "15713909584",
        email: "contact@gmail.com",
        address: "Main St 100, 19777 NY",
      },
      skills: [
        "Python",
        "Pandas",
        "SQL",
        "R",
        "AI",
        "C++",
        "Machine Learning",
        "Hadoop",
        "TensorFlow",
        "PyTorch",
        "NLP",
      ],
      highlights: [
        "Natural Language Processing with Python (Coursera)",
        "Recommendation Systems with TensorFlow on GCP (Google)",
      ],
      experience: [
        {
          title: "Senior Data Scientist",
          company: "ABC Analytics Inc.",
          location: "London",
          date: "2022 - Present",
          description: [
            "Led a team of data scientists in developing advanced machine learning models for predictive analytics",
            "Designed and implemented a recommendation system that boosted cross-selling, leading to a 20% increase in revenue",
            "Conducted A/B testing and statistical analysis to optimize product features",
          ],
        },
        {
          title: "Data Scientist",
          company: "XYZ Data Solutions",
          location: "London",
          date: "2017 - 2019",
          description: [
            "Developed and deployed machine learning models for fraud detection, reducing fraudulent transactions by 18%",
            "Conducted in-depth exploratory data analysis to identify key trends and insights",
            "Worked on data preprocessing, feature engineering, and model selection to improve model performance",
          ],
        },
        {
          title: "Data Scientist Trainee",
          company: "Data Insights Ltd.",
          location: "New York City",
          date: "2016-2017",
          description: [
            "Collaborated with external partners to integrate third-party data sources, expanding the company's data assets and enhancing predictive modeling capabilities."
          ],
        },
      ],
      education: [
        {
          title: "Master of Science in Data Science",
          university: "StellarTech University",
          location: "Starville",
          date: "2020-2022",
          description: [
            "Coursework included advanced machine learning, statistical modeling, and data visualization techniques.",
            "Thesis: 'Predictive Modeling for Customer Churn in E-commerce using Random Forest.'",
          ],
        },
        {
          title: "Bachelor of Science in Computer Science",
          university: "Evergreen State University",
          location: "Springdale",
          date: "2012-2015",
          description: [
            "Relevant coursework in database management, algorithms, and programming languages.",
            "Senior project: 'Development of a Recommender System for Movie Ratings.'",
          ],
        },
      ],
      editing: true
    };
  },
  methods: {
    updateHeadline(newValue, index) {
      this.headlines[index] = newValue;
    },
    updateProperty(event, key) {
      this[key] = event.target.innerText;
    },
    updateNestedProperty(event, key1, key2) {
      this[key1][key2] = event.target.innerText;
    },
    updateExperience(event, key, index) {
      this.experience[index][key] = event.target.innerText;
    },
    updateExperienceDescription(event, index1, index2) {
      this.experience[index1]["description"][index2] = event.target.innerText;
    },
    updateEducation(event, key, index) {
      this.education[index][key] = event.target.innerText;
    },
    updateEducationDescription(event, index1, index2) {
      this.education[index1]["description"][index2] = event.target.innerText;
    },
    addExperience() {
      this.experience.unshift({
        title: "Job Title",
        company: "Company",
        location: "Location",
        date: "date range",
        description: [
          "description"
        ]
      })
    },
    addEducation() {
      this.education.unshift({
        title: "Education title",
        university: "University",
        location: "Location",
        date: "date range",
        description: [
          "Summa cum laude, GPA 1.0"
        ]
      })
    },
    removeExperience(index) {
      this.experience.splice(index, 1);
    },
    removeEducation(index) {
      this.education.splice(index, 1);
    },
    toggleEditMode(isChecked) {
      this.editing = isChecked;
    }
  },
};
</script>

<style scoped>
#resume {
  box-shadow: rgba(50, 50, 93, 0.25) 0px 13px 27px -5px,
    rgba(0, 0, 0, 0.3) 0px 8px 16px -8px;
  width: 210mm;
}

#resume.edit-off {
  /* DIN A4 standard paper size. commonly used for resumes
  For North America letter size use width: 8.5in; height: 11in; */
  
}

@media (min-width: 1350px) {
  #resume {
  }
}

@media (min-width: 1600px) {
  #resume {
  }
}

.left-col {
  background-color: var(--background-color-left);
  color: var(--text-color-left);
  border-right: 1px solid var(--highlight-color-left);
  width: 30%;
  padding: 30px;
}

.right-col {
  background-color: var(--background-color-right);
  color: var(--text-color-right);
  width: 70%;
  padding: 30px;
}

.personal-name {
  font-weight: 300;
  color: var(--highlight-color-right);
  font-size: 28px;
  border-bottom: 1px solid var(--highlight-color-right);
  margin: 0;
  margin-left: -30px;
  padding-left: 30px;
  padding-bottom: 15px;
}

.personal-title {
  border-bottom: 1px solid var(--highlight-color-right);
  margin: 0 0 20px -30px;
  padding: 15px 0 15px 30px;
  font-weight: 300;
  font-size: 20px;
}

#resume ul {
  padding-inline-start: 16px;
  margin-block-end: 5px;
  margin-block-start: 5px;
}

.profile-pic {
  display: block;
  width: 160px;
  height: 160px;
  border: 5px solid var(--highlight-color-left);
  margin-bottom: 20px;
  object-fit: cover;
  margin-left: auto;
  margin-right: auto;
  border-radius: 50%;
}

.inner-section {
  margin-bottom: 20px;
}
</style>
