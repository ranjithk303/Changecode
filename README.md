import static org.mockito.Mockito.*;
import static org.junit.Assert.*;

import android.content.Context;
import android.content.res.Resources;
import org.junit.Before;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.mockito.*;

@RunWith(MockitoJUnitRunner.class)
public class YourClassTest {

    @Mock Context mContext;  // Mocked Context
    @Mock Resources resources;  // Mocked Resources
    @Mock InfoBookRadioButtonPreference mockListItem;  // Mocked Preference Object

    @InjectMocks YourClass yourClass;  // The class containing createListItem()

    private static final int POWER = 1;
    private static final int MANUAL = 2;

    @Before
    public void setup() {
        MockitoAnnotations.openMocks(this);

        // Mock Context's getResources()
        when(mContext.getResources()).thenReturn(resources);

        // Mock String Resources
        when(mContext.getString(R.string.power_splitgate_power_title)).thenReturn("Power Title");
        when(mContext.getString(R.string.power_splitgate_manual_title)).thenReturn("Manual Title");

        when(resources.getString(R.string.power_splitgate_power_title)).thenReturn("Power InfoBook Title");
        when(resources.getString(R.string.power_splitgate_power_infobook_body)).thenReturn("Power InfoBook Body");

        when(resources.getString(R.string.power_splitgate_manual_title)).thenReturn("Manual InfoBook Title");
        when(resources.getString(R.string.power_splitgate_manual_infobook_body)).thenReturn("Manual InfoBook Body");

        // Mock `InfoBookRadioButtonPreference` constructor
        whenNew(InfoBookRadioButtonPreference.class).withArguments(mContext).thenReturn(mockListItem);
    }

    @Test
    public void testCreateListItem_Power() {
        when(mockListItem.getKey()).thenReturn("1");
        when(mockListItem.getTitle()).thenReturn("Power Title");
        when(mockListItem.getInfoBookTitle()).thenReturn("Power InfoBook Title");
        when(mockListItem.getInfoBookBody()).thenReturn("Power InfoBook Body");

        InfoBookRadioButtonPreference listItem = yourClass.createListItem(POWER);

        assertNotNull(listItem);
        assertEquals("1", listItem.getKey());
        assertEquals("Power Title", listItem.getTitle());
        assertEquals("Power InfoBook Title", listItem.getInfoBookTitle());
        assertEquals("Power InfoBook Body", listItem.getInfoBookBody());
    }

    @Test
    public void testCreateListItem_Manual() {
        when(mockListItem.getKey()).thenReturn("2");
        when(mockListItem.getTitle()).thenReturn("Manual Title");
        when(mockListItem.getInfoBookTitle()).thenReturn("Manual InfoBook Title");
        when(mockListItem.getInfoBookBody()).thenReturn("Manual InfoBook Body");

        InfoBookRadioButtonPreference listItem = yourClass.createListItem(MANUAL);

        assertNotNull(listItem);
        assertEquals("2", listItem.getKey());
        assertEquals("Manual Title", listItem.getTitle());
        assertEquals("Manual InfoBook Title", listItem.getInfoBookTitle());
        assertEquals("Manual InfoBook Body", listItem.getInfoBookBody());
    }
}