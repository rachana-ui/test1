package net.mutualink.edge.bus.service;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.boot.test.mock.mockito.MockBean;
import org.springframework.http.MediaType;
import org.springframework.test.context.junit4.SpringRunner;
import org.springframework.test.web.servlet.MockMvc;

import java.util.Arrays;

import static org.springframework.test.web.client.match.MockRestRequestMatchers.jsonPath;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.post;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

@RunWith(SpringRunner.class)
@WebMvcTest(DataAssetController.class)
public class DataAssetControllerTest {

    private String PATH = DataAssetController.PATH;// (../dataAsset)

    private String getAll = "/getAll";

    private String id = "/id";

    private String newString = "/new";

    @Autowired
    private MockMvc mvc;

    @MockBean
    private DataAssetService dataAssetService;


    // write test cases here
    @Test
    public void getAllDataAssets_Success(){
        DataAsset asset = new DataAsset("");

        List<DataAsset> allDataAsset = Arrays.asList(asset);

        given(dataAssetService.getAll()).willReturn(allDataAsset);

        mvc.perform(get(PATH + getAll)
                .contentType(MediaType.APPLICATION_JSON))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$", hasSize(1)));
    }

    @Test
    public void getAllDataAssets_InternalServerError(){
        given(dataAssetService.getAll()).thrown.expect(NestedServletException.class);

        mvc.perform(get(PATH + getAll)
                .contentType(MediaType.APPLICATION_JSON))
               .andExpect(status().isInternalServerError());
    }

    @Test
    public void getAllDataAssets_NotFound(){

        given(dataAssetService.getData()).willReturn(null);

        mvc.perform(get(PATH + getAll)
                .contentType(MediaType.APPLICATION_JSON))
                .andExpect(status().isNotFound());
    }

    @Test
    public void getUser_Success(){

        String dataAssetId = "1234";
        DataAsset result = new DataAsset();

        given(dataAssetService.getDataAsset()).willReturn(result);

        mvc.perform(get(PATH + id, dataAssetId)
                .contentType(MediaType.APPLICATION_JSON))
                .andExpect(status().isOk());
    }

    @Test
    public void addUser_Success(){
        DataAsset result = new DataAsset();

        given(dataAssetService.getDataAsset()).willReturn(result);

        mvc.perform(post(PATH + newString)
                .content(result.toString())
                .contentType(MediaType.APPLICATION_JSON))
                .andExpect(status().isOk());
    }

}

