public class FiltroMappa
{
    public string? Regione { get; set; }
    public string? Provincia { get; set; }
    public string? Comune { get; set; }
}

public class MappeService
{
    private readonly NomeDelTuoDbContext _context;

    public MappeService(NomeDelTuoDbContext context)
    {
        _context = context;
    }

    public async Task<List<Mappa>> GetMappeAsync(FiltroMappa filtro)
    {
        var query = _context.Mappa
            .Include(m => m.IdComuneNavigation)
                .ThenInclude(c => c.IdProvinciaNavigation)
                    .ThenInclude(p => p.IdRegioneNavigation)
            .AsQueryable();

        if (!string.IsNullOrWhiteSpace(filtro.Comune))
        {
            query = query.Where(m => m.IdComuneNavigation.Denominazione.Contains(filtro.Comune));
        }

        if (!string.IsNullOrWhiteSpace(filtro.Provincia))
        {
            query = query.Where(m => m.IdComuneNavigation.IdProvinciaNavigation.Denominazione.Contains(filtro.Provincia) 
                                  || m.IdComuneNavigation.IdProvinciaNavigation.Sigla == filtro.Provincia);
        }

        if (!string.IsNullOrWhiteSpace(filtro.Regione))
        {
            query = query.Where(m => m.IdComuneNavigation.IdProvinciaNavigation.IdRegioneNavigation.Denominazione.Contains(filtro.Regione));
        }

        return await query.ToListAsync();
    }
}
