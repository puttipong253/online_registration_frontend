<script>
import styled from "vue-styled-components";

export const Wrapper = styled.div`
  width: 100%;
  max-width: 1000px;
  height: auto;
  margin: auto;
  text-align: center;
  margin-top: 20px;
  margin-bottom: 20px;
  .custom-stepper .v-stepper__step__step {
    background-color: purple !important;
    border-color: purple !important;
  }
  .v-stepper__content {
    padding: 20px 18px 24px 18px !important;
}
`;

export default {};
</script>


